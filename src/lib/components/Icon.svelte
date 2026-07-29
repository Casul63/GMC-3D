<script lang="ts">
    export type IconName =
        | "eye"
        | "eye-off"
        | "dropdown"
        | "dropdown-active"
        | "help"
        | "close"
        | "camera";

    interface Props {
        name: IconName;
        size?: string;
        color?: string;
        class?: string;
    }

    let {
        name,
        size = "size-8",
        color = "text-black",
        class: customClass = "",
    }: Props = $props();
</script>

{#if name === "eye"}
    <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="2.5"
        stroke="currentColor"
        class="{size} {color} {customClass}"
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
{:else if name === "eye-off"}
    <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="2.5"
        stroke="currentColor"
        class="{size} {color} {customClass}"
    >
        <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M3.98 8.223A10.477 10.477 0 0 0 1.934 12C3.226 16.338 7.244 19.5 12 19.5c.993 0 1.953-.138 2.863-.395M6.228 6.228A10.451 10.451 0 0 1 12 4.5c4.756 0 8.773 3.162 10.065 7.498a10.522 10.522 0 0 1-4.293 5.774M6.228 6.228 3 3m3.228 3.228 3.65 3.65m7.894 7.894L21 21m-3.228-3.228-3.65-3.65m0 0a3 3 0 1 0-4.243-4.243m4.242 4.242L9.88 9.88"
        />
    </svg>
{:else if name === "dropdown"}
    <svg
        viewBox="0 0 20 20"
        fill="currentColor"
        class="{size} {color} {customClass}"
    >
        <path
            fill-rule="evenodd"
            clip-rule="evenodd"
            d="M5.22 8.22a.75.75 0 0 1 1.06 0L10 11.94l3.72-3.72a.75.75 0 1 1 1.06 1.06l-4.25 4.25a.75.75 0 0 1-1.06 0L5.22 9.28a.75.75 0 0 1 0-1.06Z"
        />
    </svg>
{:else if name === "dropdown-active"}
    <svg
        viewBox="0 0 20 20"
        fill="currentColor"
        class="{size} {color} {customClass}"
    >
        <path
            fill-rule="evenodd"
            clip-rule="evenodd"
            d="M14.78 11.78a.75.75 0 0 1-1.06 0L10 8.06l-3.72 3.72a.75.75 0 0 1-1.06-1.06l4.25-4.25a.75.75 0 0 1 1.06 0l4.25 4.25a.75.75 0 0 1 0 1.06Z"
        />
    </svg>
{:else if name === "help"}
    <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="2.5"
        stroke="currentColor"
        class="{size} {color} {customClass}"
    >
        <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M9.879 7.519c1.171-1.025 3.071-1.025 4.242 0 1.172 1.025 1.172 2.687 0 3.712-.203.179-.43.326-.67.442-.745.361-1.45.999-1.45 1.827v.75M12 18h.01"
        />
    </svg>
{:else if name === "close"}
    <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="2.5"
        stroke="currentColor"
        class="{size} {color} {customClass}"
    >
        <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M6 18 18 6M6 6l12 12"
        />
    </svg>
{:else if name === "camera"}
    <svg
        fill="#000000"
        version="1.1"
        id="Capa_1"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        viewBox="-36.86 -36.86 442.27 442.27"
        xml:space="preserve"
        transform="matrix(1, 0, 0, 1, 0, 0)rotate(0)"
        class="{size} {color} {customClass}"
        ><g id="SVGRepo_bgCarrier" stroke-width="0"></g><g
            id="SVGRepo_tracerCarrier"
            stroke-linecap="round"
            stroke-linejoin="round"
        ></g><g id="SVGRepo_iconCarrier">
            <g>
                <g>
                    <path
                        d="M302.333,94.332l-41.874-54.176H108.095L66.22,94.332H0v234.066h368.553V94.332H302.333z M353.553,313.398H15V109.332 h58.584l41.875-54.176h137.635l41.874,54.176h58.585L353.553,313.398L353.553,313.398z"
                    ></path>
                    <path
                        d="M234.729,129.109h8.918v-15h-34.521v34.522h15v-8.515c22.102,13.715,35.568,37.653,35.568,64.042 c0,41.585-33.832,75.416-75.417,75.416v15c49.856,0,90.417-40.561,90.417-90.416C274.694,173.601,259.632,145.785,234.729,129.109 z"
                    ></path>
                    <path
                        d="M138.424,264.061c-18.509-14.109-29.564-36.009-29.564-59.903c0-41.585,33.832-75.417,75.417-75.417v-15 c-49.856,0-90.417,40.561-90.417,90.417c0,28.245,12.883,54.165,34.511,71.098h-9.469v15h34.522v-34.521h-15V264.061z"
                    ></path>
                </g> <g> </g> <g> </g> <g> </g> <g> </g> <g> </g> <g> </g>
                <g> </g> <g> </g> <g> </g> <g> </g> <g> </g> <g> </g> <g> </g>
                <g> </g> <g> </g>
            </g>
        </g></svg
    >
{/if}
