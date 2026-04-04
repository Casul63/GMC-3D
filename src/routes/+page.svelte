<script>
  import * as THREE from "three";
  import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
  import { HDRLoader } from "three/addons/loaders/HDRLoader.js";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";
  import { onMount } from "svelte";

  /**
   * @type {HTMLDivElement}
   */
  let canvasContainer;

  onMount(() => {
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);

    canvasContainer.appendChild(renderer.domElement);

    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0xeeeeee);

    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000,
    );

    const controls = new OrbitControls(camera, canvasContainer);
    controls.target.set(0, 2, 6);
    controls.update();

    const loader = new GLTFLoader();
    const hdriLoader = new HDRLoader();
    hdriLoader.load("/modern_evening_street_1k.hdr", (hdri) => {
      hdri.mapping = THREE.EquirectangularReflectionMapping;
      scene.environment = hdri;
      scene.background = hdri;
    });

    loader.load(
      "/gym_test.glb",
      (gltf) => {
        scene.add(gltf.scene);
        console.log("Model loaded");
      },
      undefined,
      (error) => {
        console.error("Error loading model:", error);
      },
    );

    const handleResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    };
    window.addEventListener("resize", handleResize);

    const animate = () => {
      renderer.render(scene, camera);
      requestAnimationFrame(animate);
    };
    animate();

    return () => {
      window.removeEventListener("resize", handleResize);
      renderer.dispose();
    };
  });
</script>

<div class="container">
  <h1>GYM MODEL TEST</h1>
  <div bind:this={canvasContainer}></div>
</div>

<style>
  :global(body) {
    margin: 0;
    overflow: hidden;
  }

  h1 {
    position: absolute;
    top: 10px;
    left: 20px;
    color: white;
    pointer-events: none;
  }
</style>
