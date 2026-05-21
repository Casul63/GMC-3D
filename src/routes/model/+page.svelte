<script lang="ts">
  import * as THREE from "three";
  import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
  import { HDRLoader } from "three/addons/loaders/HDRLoader.js";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";
  import { Reflector } from "three/examples/jsm/objects/Reflector.js";
  import { onMount } from "svelte";
  import { goto } from "$app/navigation";
  import Modal from "$lib/components/+modal.svelte";
  import Navbar from "$lib/components/+navbar.svelte";

  let config = $state({
    brightness: 1.5,
    hemiIntensity: 0.8,
    showShadows: false,
    fov: 75,
  });
  let scene = $state<THREE.Scene | null>(null);
  let tools = $state<THREE.Group | null>(null);
  let renderer = $state<THREE.WebGLRenderer | null>(null);
  let dirLight = $state<THREE.DirectionalLight | null>(null);
  let hemiLight = $state<THREE.HemisphereLight | null>(null);
  let camera = $state<THREE.PerspectiveCamera | null>(null);
  let mouse = $state<THREE.Vector2 | null>(null);
  let raycaster = $state<THREE.Raycaster | null>(null);
  let controls: any = null;

  let canvasContainer: HTMLDivElement;

  let isHidden = $state(false);
  let showModalA = $state(false);
  let showModalB = $state(false);
  let showSettings = $state(false);
  let showHelp = $state(false);
  let selectedToolName = $state("");

  let lastSelectedGroup: THREE.Object3D | null = null;
  let frameId: number;

  $effect(() => {
    if (dirLight) {
      dirLight.intensity = config.brightness;
      dirLight.castShadow = config.showShadows;
    }

    if (hemiLight) {
      hemiLight.intensity = config.hemiIntensity;
    }

    if (camera) {
      camera.fov = config.fov;
      camera.updateProjectionMatrix();
    }
  });

  onMount(() => {
    const _scene = new THREE.Scene();
    const _renderer = new THREE.WebGLRenderer();
    const _camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000,
    );
    const _hemiLight = new THREE.HemisphereLight(
      0xffffff,
      0x444444,
      config.hemiIntensity,
    );
    const _dirLight = new THREE.DirectionalLight(0xffffff, config.brightness);
    const controls = new OrbitControls(_camera, _renderer.domElement);
    const _raycaster = new THREE.Raycaster();
    const _mouse = new THREE.Vector2();

    scene = _scene;
    renderer = _renderer;
    camera = _camera;
    hemiLight = _hemiLight;
    dirLight = _dirLight;
    raycaster = _raycaster;
    mouse = _mouse;

    scene.add(hemiLight);
    scene.add(dirLight);
    dirLight.position.set(5, 10, 5);
    dirLight.castShadow = config.showShadows;
    dirLight.shadow.mapSize.set(1024, 1024);
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    _renderer.toneMapping = THREE.ACESFilmicToneMapping;
    _renderer.toneMappingExposure = 0.6;
    camera.position.set(7, 4, 5);

    canvasContainer.appendChild(renderer.domElement);

    const preventContextMenu = (event: MouseEvent) => {
      event.preventDefault();
    };

    const hdrLoader = new HDRLoader();
    hdrLoader.load("/modern_evening_street_1k.hdr", (texture) => {
      texture.mapping = THREE.EquirectangularReflectionMapping;
      _scene.environment = texture;
      _scene.background = texture;
    });

    const loader = new GLTFLoader();
    loader.load(
      "gym_restructured.glb",
      (gltf) => {
        _scene.add(gltf.scene);

        const _toolsGroup = gltf.scene.getObjectByName("Tools");

        if (_toolsGroup) {
          tools = _toolsGroup as THREE.Group;
          console.log("Tools group is true");
        } else {
          console.warn("Tools group not found");
        }
        console.log("Tools:", _scene.getObjectByName("Tools")?.children);
        console.log("Struktur Model:");
        console.log(dumpObject(gltf.scene).join("\n"));
      },
      (progress) => {
        console.log(`Loading: ${(progress.loaded / progress.total) * 100}%`);
      },
      (error) => {
        console.error("Error loading model:", error);
      },
    );

    function dumpObject(
      obj: THREE.Object3D,
      lines: String[] = [],
      isLast = true,
      prefix = "",
    ) {
      const localPrefix = isLast ? "└─" : "├─";
      lines.push(
        `${prefix}${prefix ? localPrefix : ""}${obj.name || "*no-name*"} [${obj.type}]`,
      );
      const newPrefix = prefix + (isLast ? "  " : "│ ");
      const lastNdx = obj.children.length - 1;
      obj.children.forEach((child, ndx) => {
        const isLastChild = ndx === lastNdx;
        dumpObject(child, lines, isLastChild, newPrefix);
      });
      return lines;
    }

    const render = (time: number) => {
      controls.update();
      _renderer.render(_scene, _camera);
    };

    renderer.setAnimationLoop(render);

    window.addEventListener("mousedown", handlePointerDown);
    window.addEventListener("contextmenu", preventContextMenu);
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
      window.removeEventListener("mousedown", handlePointerDown);
      window.removeEventListener("contextmenu", preventContextMenu);

      _renderer.setAnimationLoop(null);
      _renderer.dispose();
      _scene.clear();
    };
  });

  function confirmSelection() {
    goto(
      `/animations?tool=${encodeURIComponent(
        selectedToolName.replaceAll(/\d+/g, ""),
      )}`,
    );
  }

  function revealTools() {
    if (tools) {
      tools.traverse((child) => {
        child.visible = true;
      });
      isHidden = false;
      console.log("All tools revealed");
    } else {
      console.warn("No tools hidden");
    }
  }

  function handleResize() {
    if (camera && renderer) {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    }
  }

  function hideVisible() {
    if (lastSelectedGroup) {
      lastSelectedGroup.visible = false;
      isHidden = true;
      clearCurrentHighlight();
      lastSelectedGroup = null;
    }
  }

  const handlePointerDown = (event: MouseEvent) => {
    if ((event.target as HTMLElement).tagName !== "CANVAS") {
      return;
    }
    if (renderer && mouse && raycaster && camera && scene) {
      const rect = renderer.domElement.getBoundingClientRect();
      mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
      mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
      raycaster.setFromCamera(mouse, camera);
      const intersects = raycaster.intersectObjects(scene.children, true);

      if (intersects.length > 0) {
        const intersectedObject = intersects[0].object;
        let foundGroup: THREE.Object3D | null = null;

        if (intersectedObject.parent?.name === "Tools") {
          foundGroup = intersectedObject;
        } else {
          intersectedObject.traverseAncestors((ancestor) => {
            if (ancestor.parent?.name === "Tools") {
              foundGroup = ancestor;
            }
          });
        }

        if (foundGroup) {
          const group = foundGroup as THREE.Group;
          const groupName = group.name;

          if (lastSelectedGroup === group) {
            selectedToolName = groupName;
            if (event.button === 0) {
              showModalA = true;
              console.log("Modal A open:", groupName);
            } else if (event.button === 2) {
              showModalB = true;
              console.log("Modal B open:", groupName);
            }
          } else {
            clearCurrentHighlight();

            group.traverse((node) => {
              if (node instanceof THREE.Mesh) {
                if (!node.material._isCloned) {
                  node.material = node.material.clone();
                  node.material._isCloned = true;
                }
                const mat = node.material as THREE.MeshStandardMaterial;
                mat.emissive?.setRGB(0.96, 0.58, 0.16);
                mat.emissiveIntensity = 0.8;
              }
            });
            lastSelectedGroup = group;
          }
        } else {
          clearCurrentHighlight();
          lastSelectedGroup = null;
          showModalA = false;
          showModalB = false;
        }
      }
    }
  };

  function clearCurrentHighlight() {
    if (lastSelectedGroup) {
      lastSelectedGroup.traverse((node) => {
        if (node instanceof THREE.Mesh) {
          const mat = node.material as THREE.MeshStandardMaterial;

          if (mat.emissive) {
            mat.emissive.setRGB(0, 0, 0);

            mat.emissiveIntensity = 0;
          }
        }
      });
    }
  }
</script>

<Navbar></Navbar>

<div class="fixed top-18 left-6 z-100 flex flex-col items-end">
  <button
    onclick={() => (showHelp = !showHelp)}
    class="bg-white m-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:w-16 hover:h-16 hover:text-4xl transition-all"
  >
    ?
  </button>
  <Modal isOpen={showHelp} onClose={() => (showHelp = false)} title="HELP">
    <h2 class="text-center text-4xl font-bold text-red-500">DISCLAIMER</h2>
    <p class=" text-justify text-xl font-bold text-red-500 mt-2">
      Ruangan dan alat yang ditampilkan hanyalah ilustrasi dan mungkin tidak
      100% sesuai dengan kondisi aslinya
    </p>
    <ul class="list-disc pl-5 mt-2 text-justify">
      <li>
        Tekan (mobile) / klik kiri (desktop) salah satu alat untuk memberi
        highlight pada alat tersebut
      </li>
      <li>
        Tekan (mobile) / klik kiri (desktop) pada alat yang memiliki highlight
        untuk melihat animasi penggunaan alat yang tersebut
      </li>
      <li>
        Tekan dan tahan (mobile) / klik kanan (desktop) pada alat yang memiliki
        highlight untuk menyembunyikan alat tersebut
      </li>
      <li>
        Tekan ikon visibilitas untuk menampilkan kembali alat yang tersembunyi
      </li>
      <li>
        Tekan tombol dropdown untuk menampilkan pengaturan pencahayaan dan
        kamera. Edit pengaturan sampai mendapatkan hasil yang diinginkan
      </li>
    </ul>
  </Modal>
</div>
<div class="fixed top-18 right-6 z-90 flex flex-col items-end">
  <div class="flex gap-4">
    <button
      onclick={revealTools}
      title={isHidden ? "Tampilkan semua alat" : "Semua alat terlihat"}
      class="flex items-center justify-center bg-white w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:scale-120 transition-all shadow-lg"
    >
      {#if isHidden}
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="2.5"
          stroke="currentColor"
          class="size-6 text-black"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M3.98 8.223A10.477 10.477 0 0 0 1.934 12C3.226 16.338 7.244 19.5 12 19.5c.993 0 1.953-.138 2.863-.395M6.228 6.228A10.451 10.451 0 0 1 12 4.5c4.756 0 8.773 3.162 10.065 7.498a10.522 10.522 0 0 1-4.293 5.774M6.228 6.228 3 3m3.228 3.228 3.65 3.65m7.894 7.894L21 21m-3.228-3.228-3.65-3.65m0 0a3 3 0 1 0-4.243-4.243m4.242 4.242L9.88 9.88"
          />
        </svg>
      {:else}
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="2.5"
          stroke="currentColor"
          class="size-6 text-gray-400"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z"
          />
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z"
          />
        </svg>
      {/if}
    </button>
    <button
      title="setting"
      onclick={() => (showSettings = !showSettings)}
      class="flex items-center justify-center bg-white ml-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:scale-120 transition-all"
    >
      <svg
        viewBox="0 0 20 20"
        fill="currentColor"
        data-slot="icon"
        aria-hidden="true"
        class="size-8 ali text-black"
      >
        <path
          d="M5.22 8.22a.75.75 0 0 1 1.06 0L10 11.94l3.72-3.72a.75.75 0 1 1 1.06 1.06l-4.25 4.25a.75.75 0 0 1-1.06 0L5.22 9.28a.75.75 0 0 1 0-1.06Z"
          clip-rule="evenodd"
          fill-rule="evenodd"
        />
      </svg>
    </button>
  </div>

  {#if showSettings}
    <div
      class="mt-3 w-72 bg-white border-3 border-black rounded-3xl p-6 shadow-2xl max-h-[70vh] overflow-y-auto custom-scroll"
    >
      <div class="space-y-6">
        <section class="space-y-4">
          <p class="text-[10px] text-red-500 font-bold uppercase">Lighting</p>

          <div class="space-y-2">
            <div class="flex justify-between text-xs text-black">
              <span>Brightness</span>

              <span class="text-white">{config.brightness.toFixed(1)}</span>
            </div>

            <input
              type="range"
              min="0"
              max="10"
              step="0.1"
              bind:value={config.brightness}
              class="w-full accent-red-600"
            />
          </div>

          <div class="space-y-2">
            <div class="flex justify-between text-xs text-black">
              <span>Sky Intensity</span>

              <span class="text-white">{config.hemiIntensity.toFixed(1)}</span>
            </div>

            <input
              type="range"
              min="0"
              max="10"
              step="0.1"
              bind:value={config.hemiIntensity}
              class="w-full accent-red-600"
            />
          </div>
        </section>

        <section class="space-y-4 border-t border-white/5 pt-4">
          <p class="text-[10px] text-red-500 font-bold uppercase">
            Camera & Render
          </p>

          <div class="space-y-2">
            <div class="flex justify-between text-xs text-black">
              <span>Field of View</span>

              <span class="text-white">{config.fov}°</span>
            </div>

            <input
              type="range"
              min="30"
              max="110"
              step="1"
              bind:value={config.fov}
              class="w-full accent-red-600"
            />
          </div>

          <div class="flex flex-col gap-3">
            <label
              class="flex items-center justify-between cursor-pointer group"
            >
              <span
                class="text-xs text-black group-hover:text-white transition-colors"
                >Enable Shadows</span
              >

              <input
                type="checkbox"
                bind:checked={config.showShadows}
                class="w-4 h-4 accent-red-600"
              />
            </label>

            <label
              class="flex items-center justify-between cursor-pointer group"
            >
              <span
                class="text-xs text-black group-hover:text-white transition-colors"
                >Auto Rotate</span
              >
            </label>
          </div>
        </section>
      </div>
    </div>
  {/if}
</div>

<Modal isOpen={showModalA} title="" onClose={() => (showModalA = false)}>
  <h2 class="text-center text-4xl font-bold text-black">LIHAT ANIMASI?</h2>

  <div class="flex justify-between gap-8 mt-4 px-4">
    <button
      onclick={confirmSelection}
      class="bg-black w-full font-bold text-2xl rounded-sm text-white"
      >YA</button
    >

    <button
      onclick={() => (showModalA = false)}
      class="bg-white w-full font-bold text-2xl rounded-sm border-4 border-black"
      >TIDAK</button
    >
  </div>
</Modal>

<Modal isOpen={showModalB} title="HIDE" onClose={() => (showModalB = false)}>
  <h2 class="text-center text-4xl font-bold text-black">SEMBUNYIKAN ALAT?</h2>

  <div class="flex justify-between gap-8 mt-4 px-4">
    <button
      onclick={() => {
        hideVisible();
        showModalB = false;
      }}
      class="bg-black w-full font-bold text-2xl rounded-sm text-white"
      >YA</button
    >

    <button
      onclick={() => (showModalB = false)}
      class="bg-white w-full font-bold text-2xl rounded-sm border-4 border-black"
      >TIDAK</button
    >
  </div>
</Modal>

<div bind:this={canvasContainer}></div>

<style>
  :global(body) {
    margin: 0;

    padding: 0;

    overflow: hidden;
  }
</style>
