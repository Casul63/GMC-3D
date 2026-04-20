<script lang="ts">
  import * as THREE from "three";
  import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
  import { HDRLoader } from "three/addons/loaders/HDRLoader.js";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";
  import { onMount } from "svelte";

  let canvasContainer: HTMLDivElement;
  let showModalA = $state(false);
  let showModalB = $state(false);
  let selectedToolName = $state("");

  onMount(() => {
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    canvasContainer.appendChild(renderer.domElement);

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000,
    );
    camera.position.set(0, 5, 10);

    const controls = new OrbitControls(camera, renderer.domElement);
    const raycaster = new THREE.Raycaster();
    const mouse = new THREE.Vector2();

    let lastSelectedGroup: THREE.Object3D | null = null;

    const handlePointerDown = (event: MouseEvent) => {
      // 1. Hitung koordinat Raycaster
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
          const group = foundGroup as THREE.Object3D;
          const groupName = group.name;

          if (lastSelectedGroup === group) {
            selectedToolName = groupName;
            if (event.button === 0) {
              showModalA = true;
              console.log("Modal A terbuka:", groupName);
            } else if (event.button === 2) {
              showModalB = true;
              console.log("Modal B terbuka:", groupName);
            }
          } else {
            clearCurrentHighlight();

            group.traverse((node) => {
              if (node instanceof THREE.Mesh) {
                if (!(node.material as any)._isCloned) {
                  node.material = node.material.clone();
                  (node.material as any)._isCloned = true;
                }
                const mat = node.material as THREE.MeshStandardMaterial;
                mat.emissive?.setRGB(0.5, 0.5, 0.5);
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
    };

    const preventContextMenu = (event: MouseEvent) => {
      event.preventDefault();
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

    const handleResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    };

    window.addEventListener("mousedown", handlePointerDown);
    window.addEventListener("contextmenu", preventContextMenu);
    window.addEventListener("resize", handleResize);

    // Loader
    const hdrLoader = new HDRLoader();
    hdrLoader.load("/modern_evening_street_1k.hdr", (texture) => {
      texture.mapping = THREE.EquirectangularReflectionMapping;
      scene.environment = texture;
      scene.background = texture;
    });

    const loader = new GLTFLoader();
    loader.load(
      "gym_restructured.glb",
      (gltf) => {
        const root = gltf.scene;
        scene.add(root);
        console.log("Tools:", scene.getObjectByName("Tools")?.children);

        console.log("Struktur Model:");
        console.log(dumpObject(root).join("\n"));
      },
      (xhr) => {
        console.log(`Loading: ${(xhr.loaded / xhr.total) * 100}%`);
      },
      (error) => {
        console.error("Error loading model:", error);
      },
    );

    let frameId: number;
    const animate = () => {
      controls.update();
      renderer.render(scene, camera);
      frameId = requestAnimationFrame(animate);
    };
    animate();

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

    // Cleanup
    return () => {
      window.removeEventListener("resize", handleResize);
      window.removeEventListener("mousedown", handlePointerDown);
      window.removeEventListener("contextmenu", preventContextMenu);
      cancelAnimationFrame(frameId);
      renderer.dispose();
      scene.clear();
    };
  });
</script>

<div class="container">
  <h1>GYM MODEL TEST</h1>
  {#if showModalA}
    <div class="modal">
      <h2>Modal A: Detail {selectedToolName}</h2>
      <button onclick={() => (showModalA = false)}>Tutup</button>
    </div>
  {/if}

  {#if showModalB}
    <div class="modal">
      <h2>Modal B: Pengaturan {selectedToolName}</h2>
      <button onclick={() => (showModalB = false)}>Tutup</button>
    </div>
  {/if}
  <div bind:this={canvasContainer}></div>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    overflow: hidden;
  }
  .container {
    width: 100vw;
    height: 100vh;
  }
  h1 {
    position: absolute;
    top: 10px;
    left: 20px;
    color: white;
    pointer-events: none;
    z-index: 10;
    font-family: sans-serif;
  }
  .modal {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: white;
    padding: 20px;
    z-index: 100;
    border-radius: 8px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
  }
</style>
