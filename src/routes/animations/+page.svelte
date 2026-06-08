<script lang="ts">
    import { page } from "$app/state";
    import { onMount } from "svelte";
    import * as THREE from "three";
    import { OrbitControls } from "three/addons/controls/OrbitControls.js";
    import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
    import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader.js";
    import { HDRLoader } from "three/addons/loaders/HDRLoader.js";
    import Modal from "$lib/components/+modal.svelte";

    const toolName = $derived(page.url.searchParams.get("tool") || "");
    let canvasContainer: HTMLDivElement;
    let showModalAnim = $state(false);
    let showModalHelp = $state(false);

    let isPlaying = $state(false);
    let currentAnimationName = $state("");
    let thumbStartIndex = $state(0);

    let animationsList = $state<THREE.AnimationClip[]>([]);

    let scene: THREE.Scene;
    let mixer: THREE.AnimationMixer | null = null;
    let currentAction: THREE.AnimationAction | null = null;
    const loader = new GLTFLoader();

    let isLoadingAnimasi = $state(true);
    let loadingProgressText = $state("0%");

    function changeAnimation(clip: THREE.AnimationClip) {
        if (!mixer) return;

        mixer.stopAllAction();

        currentAction = mixer.clipAction(clip);
        currentAction.reset();
        currentAction.setEffectiveWeight(1);
        currentAction.setEffectiveTimeScale(1);

        currentAction.play();
        currentAction.paused = true;
        isPlaying = false;
        currentAnimationName = clip.name;
    }

    function selectAnimation(clip: THREE.AnimationClip) {
        changeAnimation(clip);
        showModalAnim = false;
    }

    function toggleAnimation() {
        if (!currentAction) return;
        currentAction.paused = isPlaying;
        isPlaying = !isPlaying;
    }

    function handleImageError(e: Event) {
        const img = e.currentTarget as HTMLImageElement;
        img.src = "/error.jpg";
    }

    onMount(() => {
        scene = new THREE.Scene();
        // scene.background = new THREE.Color("rgb(82, 82, 82)");

        const gradientCanvas = document.createElement("canvas");
        gradientCanvas.width = window.innerWidth;
        gradientCanvas.height = window.innerHeight;
        const context = gradientCanvas.getContext("2d");
        if (context) {
            const gradient = context.createLinearGradient(
                0,
                0,
                0,
                gradientCanvas.height,
            );
            gradient.addColorStop(0, "#015494");
            gradient.addColorStop(1, "#0090ff");

            context.fillStyle = gradient;
            context.fillRect(0, 0, gradientCanvas.width, gradientCanvas.height);
        }
        const backgroundTexture = new THREE.CanvasTexture(gradientCanvas);
        scene.background = backgroundTexture;

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        canvasContainer.appendChild(renderer.domElement);

        const camera = new THREE.PerspectiveCamera(
            75,
            window.innerWidth / window.innerHeight,
            0.1,
            1000,
        );
        // camera.rotation.y = Math.PI / 2;
        camera.position.set(0, 2, 2);
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;

        const hdrLoader = new HDRLoader();
        hdrLoader.load("/modern_evening_street_1k.hdr", (texture) => {
            texture.mapping = THREE.EquirectangularReflectionMapping;
            scene.environment = texture;
            // scene.background = texture;
        });

        if (toolName) {
            const modelPath = `/animations/${toolName}/${toolName}.glb`;

            loader.load(
                modelPath,
                (gltf) => {
                    scene.add(gltf.scene);
                    mixer = new THREE.AnimationMixer(gltf.scene);

                    animationsList = gltf.animations;
                    console.log(animationsList);

                    if (animationsList.length > 0) {
                        changeAnimation(animationsList[0]);
                        if (currentAction) currentAction.paused = true;
                        isPlaying = false;
                    }
                    isLoadingAnimasi = false;
                },
                (progress) => {
                    if (progress.total > 0) {
                        const percent = Math.round(
                            (progress.loaded / progress.total) * 100,
                        );
                        loadingProgressText = `${percent}%`;
                    } else {
                        loadingProgressText = "Memproses aset...";
                    }
                },
            );
        }

        const timer = new THREE.Timer();
        let frameId: number;
        const animate = () => {
            timer.update();
            const delta = timer.getDelta();
            if (mixer) mixer.update(delta);
            controls.update();
            renderer.render(scene, camera);
            frameId = requestAnimationFrame(animate);
        };
        animate();

        const handleResize = () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        };
        window.addEventListener("resize", handleResize);

        return () => {
            if (renderer.domElement && renderer.domElement.parentNode) {
                renderer.domElement.parentNode.removeChild(renderer.domElement);
            }
            renderer.dispose();
            window.removeEventListener("resize", handleResize);
            cancelAnimationFrame(frameId);
        };
    });
</script>

<Modal isOpen={isLoadingAnimasi} onClose={() => (isLoadingAnimasi = false)}>
    <div class="inset-0 flex flex-col items-center justify-center py-2">
        <div
            class="w-16 h-16 border-4 border-t-red-600 border-r-transparent border-b-red-600 border-l-transparent rounded-full animate-spin mb-4"
        ></div>

        <h3
            class="text-white text-2xl font-black tracking-widest uppercase animate-pulse"
        >
            Loading Animasi
        </h3>
        <p class="text-red-500 font-mono text-xl font-bold">
            {loadingProgressText}
        </p>

        <span class="text-gray-400 text-xs mt-6 max-w-xs text-center">
            Download / Rendering Animasi. Harap Tunggu Sebentar
        </span>
    </div></Modal
>

<div class="relative w-full h-screen overflow-hidden bg-black">
    <div
        class="absolute inset-0 z-10 pointer-events-none p-6 flex justify-between"
    >
        <div class="pointer-events-auto">
            <a
                href="/model"
                class="flex pb-1 items-center justify-center bg-white m-auto w-12 h-12 rounded-full border-3 border-black text-2xl font-extrabold hover:scale-125 transition-all duration-300 transform"
            >
                ←
            </a>
        </div>

        <button
            onclick={() => (showModalAnim = true)}
            class="pointer-events-auto flex flex-col items-center rounded-3xl h-fit gap-4 hover:scale-125 transition-transform"
        >
            <div class="flex flex-col gap-4">
                <div
                    class="w-30 h-30 rounded-3xl border-6 border-black overflow-hidden transition-all"
                >
                    <img
                        src="/animations/{toolName}/{currentAnimationName.replaceAll(
                            ' ',
                            '_',
                        )}.jpg"
                        alt={currentAnimationName}
                        class="w-full h-full object-cover"
                    />
                </div>
            </div>
        </button>
    </div>

    <div
        class="absolute bottom-20 md:bottom-10 left-1/2 -translate-x-1/2 z-20 flex flex-col items-center gap-4 pointer-events-none"
    >
        <div class="bg-black backdrop-blur-md px-6 py-2 rounded-full border">
            <h1
                class="text-white text-center font-bold tracking-widest uppercase text-sm"
            >
                <span class="text-red-400 ml-2"
                    >{currentAnimationName.replaceAll("_", " ")}</span
                >
            </h1>
        </div>

        <button
            class="bg-white text-black border-4 border-black py-3.5 px-10 rounded-full font-black text-lg tracking-wider transition-all duration-300 ease-[cubic-bezier(0.175,0.885,0.32,1.275)] shadow-[0_10px_30px_rgba(0,0,0,0.5)] hover:scale-105 hover:-translate-y-1 pointer-events-auto"
            onclick={toggleAnimation}
        >
            {isPlaying ? "⏸" : "▶"}
        </button>
    </div>

    <div bind:this={canvasContainer} class="w-full h-full"></div>

    <Modal
        isOpen={showModalAnim}
        title="Daftar Gerakan"
        onClose={() => {
            showModalAnim = false;
        }}
    >
        <div class="grid grid-cols-2 sm:grid-cols-3 gap-3">
            {#each animationsList as anim}
                <button
                    onclick={() => selectAnimation(anim)}
                    class="group flex flex-col items-center gap-2 p-3 rounded-2xl border-4 border-black hover:border-red-500 hover:bg-red-500/5 transition-all bg-white/5"
                >
                    <div
                        class="w-full aspect-square rounded-xl overflow-hidden bg-black/40"
                    >
                        <img
                            src="/animations/{toolName}/{anim.name
                                .replaceAll(/\s+/g, '_')
                                .replaceAll(/\d+/g, '')}.jpg"
                            alt={anim.name}
                            class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500"
                            onerror={handleImageError}
                        />
                    </div>
                    <span
                        class="text-lg font-bold uppercase tracking-tighter text-black group-hover:text-red-400 text-center line-clamp-2"
                    >
                        {anim.name.replaceAll("_", " ")}
                    </span>
                </button>
            {/each}
        </div>
    </Modal>
</div>
