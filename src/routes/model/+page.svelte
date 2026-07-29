<script lang="ts">
  import * as THREE from "three";
  import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
  import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader.js";
  import { HDRLoader } from "three/addons/loaders/HDRLoader.js";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";
  import { Reflector } from "three/examples/jsm/objects/Reflector.js";
  import { onMount } from "svelte";
  import { goto } from "$app/navigation";
  import Modal from "$lib/components/+modal.svelte";
  import Icon from "$lib/components/Icon.svelte";

  let config = $state({
    brightness: 1.5,
    hemiIntensity: 0.8,
    fov: 75,
    cameraHeightLock: true,
    minY: 0.3,
  });
  let scene = $state<THREE.Scene | null>(null);
  let tools = $state<THREE.Group | null>(null);
  let renderer = $state<THREE.WebGLRenderer | null>(null);
  let dirLight = $state<THREE.DirectionalLight | null>(null);
  let hemiLight = $state<THREE.HemisphereLight | null>(null);

  let camera = $state<THREE.PerspectiveCamera | null>(null);
  let resetCamera = $state<boolean>(false);
  let cameraPosition = $state({ x: 0, y: 0, z: 0 });

  let point = $state<THREE.Vector2 | null>(null);
  let raycaster = $state<THREE.Raycaster | null>(null);
  let controls = $state<OrbitControls | null>(null);

  let intersectsLive = $state("");
  let targetDistance = $state<number | null>(null);
  let lastHitWall: THREE.Object3D | null = null;

  let canvasContainer: HTMLDivElement;

  let isHidden = $state(false);
  let showRedirectAnimation = $state(false);
  let showHideTool = $state(false);
  let showModal = $state(false);
  let showSettings = $state(false);
  let showHelp = $state(false);
  let lastSelectedToolName = $state("");

  let lastSelectedTool: THREE.Object3D | null = null;

  let frameId: number;

  let isLoadingModel = $state(true);
  let loadingProgressText = $state("0%");

  $effect(() => {
    if (dirLight) {
      dirLight.intensity = config.brightness;
    }

    if (hemiLight) {
      hemiLight.intensity = config.hemiIntensity;
    }

    if (camera) {
      camera.fov = config.fov;
      camera.updateProjectionMatrix();
    }

    if (controls) {
      if (config.cameraHeightLock) {
        // Mengunci kamera agar maksimal sejajar lantai (90 derajat)
        controls.maxPolarAngle = Math.PI / 2 - 0.01; // Dikurangi sedikit agar tidak bergetar saat menyentuh batas bawah
      } else {
        // Mengembalikan ke pengaturan bawaan (bisa berputar ke bawah objek)
        controls.maxPolarAngle = Math.PI;
      }
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
    const _controls = new OrbitControls(_camera, _renderer.domElement);
    _controls.enableDamping = true;

    controls = _controls;
    const _raycaster = new THREE.Raycaster();
    const _point = new THREE.Vector2();

    scene = _scene;
    renderer = _renderer;
    camera = _camera;
    hemiLight = _hemiLight;
    dirLight = _dirLight;
    raycaster = _raycaster;
    point = _point;

    const debugRaycaster = new THREE.Raycaster();
    const centerScreen = new THREE.Vector2(0, 0);

    scene.add(hemiLight);
    scene.add(dirLight);
    dirLight.position.set(5, 10, 5);
    dirLight.shadow.mapSize.set(1024, 1024);
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    _renderer.toneMapping = THREE.ACESFilmicToneMapping;
    _renderer.toneMappingExposure = 0.6;
    camera.position.set(4, 3, 3);
    // camera.near = 1;

    canvasContainer.appendChild(renderer.domElement);

    const hdrLoader = new HDRLoader();
    hdrLoader.load("/modern_evening_street_1k.hdr", (texture) => {
      texture.mapping = THREE.EquirectangularReflectionMapping;
      _scene.environment = texture;
      _scene.background = texture;
    });

    const loader = new GLTFLoader();
    const dracoLoader = new DRACOLoader();
    dracoLoader.setDecoderPath("/draco/");
    loader.setDRACOLoader(dracoLoader);
    loader.load(
      "gym_restructured.glb",
      (gltf) => {
        _scene.add(gltf.scene);

        const toolsGroup = gltf.scene.getObjectByName("Tools");
        const wall = gltf.scene.getObjectByName("Wall");

        // if (wall) {
        //   wall.traverse((child) => {
        //     child.raycast = () => {};
        //   });
        // }

        if (toolsGroup) {
          tools = toolsGroup as THREE.Group;
          console.log("Tools group is true");
        } else {
          console.warn("Tools group not found");
        }

        console.log("Tools:", _scene.getObjectByName("Tools")?.children);
        console.log("Struktur Model:");
        console.log(dumpObject(gltf.scene).join("\n"));

        isLoadingModel = false;
      },
      (progress) => {
        if (progress.total > 0) {
          const percent = Math.round((progress.loaded / progress.total) * 100);
          loadingProgressText = `${percent}%`;
        } else {
          loadingProgressText = "Memproses aset...";
        }
      },
      (error) => {
        console.error("Error loading model:", error);
        loadingProgressText = "Gagal memuat model 3D.";
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

    const renderLoop = () => {
      _controls.update();

      if (resetCamera) {
        _camera.position.set(4, 3, 3);
        resetCamera = false;
      }

      if (_scene && _camera) {
        debugRaycaster.setFromCamera(centerScreen, _camera);
        const liveHits = debugRaycaster.intersectObjects(_scene.children, true);

        if (liveHits.length > 0) {
          const closestHit = liveHits[0];
          intersectsLive =
            closestHit.object.parent?.name || `[${closestHit.object.type}]`;
          targetDistance = closestHit.distance;

          let currentWallGroup: THREE.Object3D | null = null;
          if (closestHit.object.name === "Wall") {
            currentWallGroup = closestHit.object;
          } else {
            closestHit.object.traverseAncestors((ancestor) => {
              if (ancestor.name === "Wall") {
                currentWallGroup = ancestor;
              }
            });
          }

          if (currentWallGroup) {
            const isTooClose = closestHit.distance < 1.5;

            if (lastHitWall && lastHitWall !== currentWallGroup) {
              setWallOpacity(lastHitWall, 1.0, false);
            }

            setWallOpacity(
              currentWallGroup,
              isTooClose ? 0.1 : 1.0,
              isTooClose,
            );
            lastHitWall = currentWallGroup;
          } else {
            if (lastHitWall) {
              setWallOpacity(lastHitWall, 1.0, false);
              lastHitWall = null;
            }
          }
        } else {
          intersectsLive = "Tidak ada objek";
          targetDistance = null;
          if (lastHitWall) {
            setWallOpacity(lastHitWall, 1.0, false);
            lastHitWall = null;
          }
        }
      }

      if (config.cameraHeightLock && _camera.position.y < config.minY) {
        _camera.position.y = config.minY;
        _controls.enablePan = false;
        _controls.update();
      }
      if (config.cameraHeightLock && _camera.position.y > config.minY) {
        _controls.enablePan = true;
        _controls.update();
      }

      _renderer.render(_scene, _camera);
      cameraPosition = {
        x: _camera.position.x,
        y: _camera.position.y,
        z: _camera.position.z,
      };
    };

    _renderer.setAnimationLoop(renderLoop);

    _renderer.domElement.addEventListener("pointerup", handlePointerUp);
    window.addEventListener("contextmenu", preventContextMenu);
    window.addEventListener("resize", handleResize);

    return () => {
      if (_renderer) {
        _renderer.setAnimationLoop(null);
        if (_renderer.domElement && _renderer.domElement.parentNode) {
          _renderer.domElement.removeEventListener(
            "pointerup",
            handlePointerUp,
          );
          _renderer.domElement.parentNode.removeChild(_renderer.domElement);
        }
        _renderer.dispose();
      }
      window.removeEventListener("contextmenu", preventContextMenu);
      window.removeEventListener("resize", handleResize);

      _renderer.setAnimationLoop(null);
      _renderer.dispose();
      _scene.clear();
    };
  });

  function setWallOpacity(
    wallGroup: THREE.Object3D,
    opacityValue: number,
    isTransparent: boolean,
  ) {
    wallGroup.traverse((node) => {
      if (node instanceof THREE.Mesh) {
        if (!node.material._isCloned) {
          node.material = node.material.clone();
          node.material._isCloned = true;
        }

        const mat = node.material as THREE.MeshStandardMaterial;
        mat.transparent = isTransparent;
        mat.opacity = opacityValue;

        mat.needsUpdate = true;
      }
    });
  }

  function preventContextMenu(event: MouseEvent) {
    event.preventDefault();
  }

  function redirectAnimation() {
    showModal = false;
    goto(
      `/animations?tool=${encodeURIComponent(
        lastSelectedToolName.replaceAll(/\d+/g, ""),
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
    if (lastSelectedTool) {
      lastSelectedTool.visible = false;
      isHidden = true;
      clearCurrentHighlight();
      lastSelectedTool = null;
    }
  }

  function handleToolRaycast(
    clientX: number,
    clientY: number,
  ): THREE.Object3D | null {
    if (!renderer || !point || !raycaster || !camera || !scene) return null;

    const rect = renderer.domElement.getBoundingClientRect();
    point.x = ((clientX - rect.left) / rect.width) * 2 - 1;
    point.y = -((clientY - rect.top) / rect.height) * 2 + 1;
    raycaster.setFromCamera(point, camera);
    const intersects = raycaster.intersectObjects(scene.children, true);

    for (const intersect of intersects) {
      const intersectedObject = intersect.object;
      if (intersectedObject.name === "Wall") {
        continue;
      }
      if (intersectedObject.parent?.name === "Tools") {
        return intersectedObject;
      }
      let foundTool: THREE.Object3D | null = null;
      intersectedObject.traverseAncestors((ancestor) => {
        if (ancestor.parent?.name === "Tools") {
          foundTool = ancestor;
        }
      });

      if (foundTool) {
        return foundTool;
      }
    }
    return null;
  }

  function handleHighlight(group: THREE.Group) {
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
    lastSelectedTool = group;
  }

  function handlePointerUp(event: PointerEvent) {
    const target = event.target as HTMLElement;
    if (target.tagName !== "CANVAS") return;
    const foundGroup = handleToolRaycast(event.clientX, event.clientY);

    if (foundGroup) {
      const group = foundGroup as THREE.Group;
      if (lastSelectedTool === group) {
        setTimeout(() => {
          showModal = true;
        }, 30);
      }

      handleHighlight(group);
      lastSelectedToolName = group.name;
    } else {
      clearCurrentHighlight();
      lastSelectedTool = null;
    }
  }

  function clearCurrentHighlight() {
    if (lastSelectedTool) {
      lastSelectedTool.traverse((node) => {
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

<!-- Debug Div -->
<!-- <div class="fixed bg-black/50 text-white bottom-10 left-10">
  <p>
    FOV: {config.fov}° | X: {cameraPosition.x.toFixed(2)}
    Y: {cameraPosition.y.toFixed(2)}
    Z: {cameraPosition.z.toFixed(2)}
    Ray : {intersectsLive}

    {#if targetDistance !== null}
      Jarak ke Objek: {targetDistance.toFixed(2)}m
    {:else}
      Jarak ke Objek: -
    {/if}
  </p>
</div> -->

<div class="fixed top-18 left-6 z-100 flex flex-col items-end">
  <button
    onclick={() => (showHelp = !showHelp)}
    class="bg-white m-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:w-16 hover:h-16 hover:text-4xl transition-all"
  >
    ?
  </button>
  <Modal
    isOpen={showHelp}
    onClose={() => {
      showHelp = false;
    }}
    title="HELP"
  >
    <h2 class="text-center text-4xl font-bold text-red-500">DISCLAIMER</h2>
    <p class=" text-justify text-xl font-bold text-red-500 mt-2">
      Ruangan dan alat yang ditampilkan hanyalah ilustrasi dan mungkin tidak
      100% sesuai dengan kondisi aslinya
    </p>
    <ul class="list-disc pl-5 mt-2 text-justify">
      <li>Tekan salah satu alat untuk memberi highlight pada alat tersebut</li>
      <li>
        Tekan alat yang memiliki highlight untuk melihat modal dengan pilihan
        "sembunyikan alat?" dan "lihat animasi?"
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
      onclick={() => {
        resetCamera = true;
      }}
      class="flex items-center justify-center bg-white ml-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:scale-120 transition-all"
    >
      <Icon name="camera"></Icon>
    </button>
    <button
      onclick={revealTools}
      title={isHidden ? "Tampilkan semua alat" : "Semua alat terlihat"}
      class="flex items-center justify-center bg-white w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:scale-120 transition-all shadow-lg"
    >
      {#if isHidden}
        <Icon name="eye"></Icon>
      {:else}
        <Icon name="eye-off" size="size-6" color="text-black/50"></Icon>
      {/if}
    </button>
    <button
      title="setting"
      onclick={() => (showSettings = !showSettings)}
      class="flex items-center justify-center bg-white ml-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-bold hover:scale-120 transition-all"
    >
      {#if showSettings}
        <Icon name="dropdown-active"></Icon>
      {:else}
        <Icon name="dropdown"></Icon>
      {/if}
    </button>
  </div>

  <Modal isOpen={isLoadingModel} onClose={() => (isLoadingModel = false)}>
    <div class="inset-0 flex flex-col items-center justify-center py-2">
      <div
        class="w-16 h-16 border-4 border-t-red-600 border-r-transparent border-b-red-600 border-l-transparent rounded-full animate-spin mb-4"
      ></div>

      <h3
        class="text-black text-2xl font-black tracking-widest uppercase animate-pulse"
      >
        Loading Ruangan Gym
      </h3>
      <p class="text-red-500 font-mono text-xl font-bold">
        {loadingProgressText}
      </p>

      <span class="text-gray-400 text-xs mt-6 max-w-xs text-center">
        Download / Rendering Model. Harap Tunggu Sebentar
      </span>
    </div></Modal
  >

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
            <div class="flex items-center justify-between pt-2">
              <span class="text-xs text-black">Batas Kamera</span>
              <label class="relative inline-flex items-center cursor-pointer">
                <input
                  type="checkbox"
                  bind:checked={config.cameraHeightLock}
                  class="sr-only peer"
                />
                <div
                  class="w-11 h-6 bg-gray-200 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-red-600"
                ></div>
              </label>
            </div>
          </div>
        </section>
      </div>
    </div>
  {/if}
</div>

<Modal isOpen={showModal} title="" onClose={() => (showModal = false)}>
  <button
    onclick={() => (showModal = false)}
    class=" bg-white border-3 border-black w-10 h-10 rounded-md pb-1 mx-auto hover:bg-black hover:text-white transition-all shadow-md cursor-pointer"
    aria-label="Close modal"
    >x
  </button>
  <div class="flex justify-between gap-4 mt-4 h-30 md:h-60">
    <button
      onclick={() => {
        hideVisible();
        showModal = false;
      }}
      class="bg-black w-full font-bold text-md md:text-5xl rounded-sm hover:bg-red-700 text-white transition-all hover:text-black"
      >SEMBUNYIKAN <br />ALAT?</button
    >

    <button
      onclick={redirectAnimation}
      class="bg-white w-full font-bold text-md md:text-5xl rounded-sm border-4 hover:border-red-700 hover:bg-red-700 hover:text-white transition-all border-black"
      >LIHAT <br />ANIMASI?</button
    >
  </div>
</Modal>

<div bind:this={canvasContainer} class="overflow-hidden"></div>
