<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    import { slide } from 'svelte/transition';

    const dispatch = createEventDispatcher();

    let startY:number;

    const handleMouseDown = (event:MouseEvent) => {
        startY = event.clientY;

        document.addEventListener('mousemove', handleMouseMove);
        document.addEventListener('mouseup', handleMouseUp);
    };

    const handleMouseMove = (event:MouseEvent) => {
        if (event.clientY > startY + 10) {
            dispatch('close');
            cleanup();
        }
    };

    const handleMouseUp = () => {
        cleanup();
    };

    const cleanup = () => {
        document.removeEventListener('mousemove', handleMouseMove);
        document.removeEventListener('mouseup', handleMouseUp);
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

<div class="popup" transition:slide="{{ duration: 300}}">
    <div class="pull-bar" on:mousedown={handleMouseDown}></div>
    <!-- Popup Content -->
    <div class="content">
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </p><p>
        Curabitur pretium tincidunt lacus. Nulla gravida orci a odio. Nullam varius, turpis et commodo pharetra, est eros bibendum elit, nec luctus magna felis sollicitudin mauris. Integer in mauris eu nibh euismod gravida. Duis ac tellus et risus vulputate vehicula. Donec lobortis risus a elit. Etiam tempor. Ut ullamcorper, ligula eu tempor congue, eros est euismod turpis, id tincidunt sapien risus a quam. Maecenas fermentum consequat mi. Donec fermentum. Pellentesque malesuada nulla a mi. Duis sapien sem, aliquet nec, commodo eget, consequat quis, neque. Aliquam faucibus, elit ut dictum aliquet, felis nisl adipiscing sapien, sed malesuada diam lacus eget erat.
    </p>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </p><p>
        Curabitur pretium tincidunt lacus. Nulla gravida orci a odio. Nullam varius, turpis et commodo pharetra, est eros bibendum elit, nec luctus magna felis sollicitudin mauris. Integer in mauris eu nibh euismod gravida. Duis ac tellus et risus vulputate vehicula. Donec lobortis risus a elit. Etiam tempor. Ut ullamcorper, ligula eu tempor congue, eros est euismod turpis, id tincidunt sapien risus a quam. Maecenas fermentum consequat mi. Donec fermentum. Pellentesque malesuada nulla a mi. Duis sapien sem, aliquet nec, commodo eget, consequat quis, neque. Aliquam faucibus, elit ut dictum aliquet, felis nisl adipiscing sapien, sed malesuada diam lacus eget erat.
    </p>
    </div>
    <div class="action-bar">
        <button>&lt; Previous</button>
        <button>Next &gt;</button>
    </div>
</div>
