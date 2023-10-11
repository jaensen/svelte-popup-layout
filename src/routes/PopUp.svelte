<script context="module" lang="ts">
    import {writable} from 'svelte/store';

    export type PopupControls = {
        close: (() => void) | null;
        open: (() => void) | null;
    };

    export const popupControls = writable<PopupControls>({
        close: null,
        open: null
    });
</script>
<script lang="ts">
    import {createEventDispatcher} from 'svelte';
    import {tweened} from 'svelte/motion';
    import {expoOut} from 'svelte/easing';

    const dispatch = createEventDispatcher();

    let initialY: number;
    let lastY: number;
    let currentY: number;
    let lastTime: number;
    let velocity: number = 0;
    let popupHeight: number = 4096;

    const y = tweened(popupHeight, {duration: 300, easing: expoOut});

    $: {
        let opacityValue = 1 - ($y / popupHeight);
        opacityValue = opacityValue < 0 ? 0 : opacityValue > 1 ? 1 : opacityValue;  // Clamping the value between 0 and 1
        dispatch('overlayOpacity', { opacity: opacityValue });
    }

    let popup: HTMLDivElement;

    $popupControls.open = () => {
        y.set(0, {duration: 300, easing: expoOut});  // Animate opening
    };
    $popupControls.close = async () => {
        popupHeight = popup.offsetHeight;
        await y.set(popupHeight, {duration: 300, easing: expoOut});
        dispatch('close');
    };

    const handleStart = (event: MouseEvent | TouchEvent) => {
        event.preventDefault();
        initialY = lastY = currentY = getY(event);
        lastTime = performance.now();
        popupHeight = popup.offsetHeight;
        startVelocityTracking();

        document.addEventListener('mousemove', handleMove);
        document.addEventListener('mouseup', handleEnd);
        document.addEventListener('touchmove', handleMove);
        document.addEventListener('touchend', handleEnd);
    };

    const handleMove = (event: MouseEvent | TouchEvent) => {
        currentY = getY(event);
        const deltaY = currentY - initialY;
        y.set(deltaY > 0 ? deltaY : 0, {duration: 0});  // Update position without animation
    };

    const handleEnd = () => {
        stopVelocityTracking();
        document.removeEventListener('mousemove', handleMove);
        document.removeEventListener('mouseup', handleEnd);
        document.removeEventListener('touchmove', handleMove);
        document.removeEventListener('touchend', handleEnd);

        if (velocity > 0.05) {
            y.set(popupHeight, {duration: 300, easing: expoOut});  // Animate closing
        } else {
            y.set(0, {duration: 300, easing: expoOut});  // Animate opening
        }
    };

    let velocityInterval: NodeJS.Timeout;
    const startVelocityTracking = () => {
        velocityInterval = setInterval(() => {
            const now = performance.now();
            velocity = (currentY - lastY) / (now - lastTime);
            lastY = currentY;
            lastTime = now;
        }, 50);  // Update velocity every 50ms
    };

    const stopVelocityTracking = () => {
        clearInterval(velocityInterval);
    };

    const getY = (event: MouseEvent | TouchEvent) => {
        return event instanceof MouseEvent ? event.clientY : event.touches[0].clientY;
    };
</script>

<style>
    .popup {
        position: fixed;
        bottom: 0;
        left: 0;
        width: 100%;
        max-height: 100%;
        display: flex;
        flex-direction: column;
        background-color: white;
        border-top: 5px solid #ccc;
        z-index: 2; /* Ensure popup is above overlay */
        transform: translateY(var(--y)); /* use the CSS variable to set the position */
    }

    .pull-bar {
        width: 100%;
        height: 40px;
        background-color: #ccc;
        cursor: pointer;
    }

    .content {
        overflow-y: auto;
        flex-grow: 1;
    }

    .action-bar {
        display: flex;
        justify-content: space-around;
        padding: 10px;
        background-color: #f9f9f9;
        border-top: 1px solid #ccc;
    }
</style>

<div class="popup" bind:this={popup} style="--y: { $y }px">
    <div class="pull-bar" on:mousedown={handleStart} on:touchstart={handleStart}></div>
    <div class="content">
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et
            dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex
            ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat
            nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit
            anim id est laborum.
        </p>
        <p>
            Curabitur pretium tincidunt lacus. Nulla gravida orci a odio. Nullam varius, turpis et commodo pharetra, est
            eros bibendum elit, nec luctus magna felis sollicitudin mauris. Integer in mauris eu nibh euismod gravida.
            Duis ac tellus et risus vulputate vehicula. Donec lobortis risus a elit. Etiam tempor. Ut ullamcorper,
            ligula eu tempor congue, eros est euismod turpis, id tincidunt sapien risus a quam. Maecenas fermentum
            consequat mi. Donec fermentum. Pellentesque malesuada nulla a mi. Duis sapien sem, aliquet nec, commodo
            eget, consequat quis, neque. Aliquam faucibus, elit ut dictum aliquet, felis nisl adipiscing sapien, sed
            malesuada diam lacus eget erat.
        </p>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et
            dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex
            ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat
            nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit
            anim id est laborum.
        </p>
        <p>
            Curabitur pretium tincidunt lacus. Nulla gravida orci a odio. Nullam varius, turpis et commodo pharetra, est
            eros bibendum elit, nec luctus magna felis sollicitudin mauris. Integer in mauris eu nibh euismod gravida.
            Duis ac tellus et risus vulputate vehicula. Donec lobortis risus a elit. Etiam tempor. Ut ullamcorper,
            ligula eu tempor congue, eros est euismod turpis, id tincidunt sapien risus a quam. Maecenas fermentum
            consequat mi. Donec fermentum. Pellentesque malesuada nulla a mi. Duis sapien sem, aliquet nec, commodo
            eget, consequat quis, neque. Aliquam faucibus, elit ut dictum aliquet, felis nisl adipiscing sapien, sed
            malesuada diam lacus eget erat.
        </p>
    </div>
    <div class="action-bar">
        <button>&lt; Previous</button>
        <button>Next &gt;</button>
    </div>
</div>
