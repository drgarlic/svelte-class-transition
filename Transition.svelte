<script>
    import { onMount } from 'svelte';

    export let toggle = undefined;
    export let transitions = '';
    export let inTransition = '';
    export let outTransition = inTransition;
    export let inState = '';
    export let onState = '';
    export let outState = inState;
    export let offVisible = false;

    let div;
    let slot;
    let slotClasses;
    let parent;

    const STATE = {
        IDLE: 0,
        ENTERING: 1,
        LEAVING: 2,
    };

    let state = STATE.IDLE;

    onMount(() => {
        slot = div.nextElementSibling;
        slotClasses = slot.classList.value;

        if (toggle === undefined) {
            slot.hidden = true;

            if (document.readyState === 'complete') {
                initTogglelessTransition();
            } else {
                window.addEventListener('load', () => {
                    initTogglelessTransition();
                }, { once: true });
            }
        } else {
            initTransition();
        }
    });

    const initTogglelessTransition = () => {
        searchParentTransition();
        toggle = parent ? parent.toggle : false;
        initTransition();
        if (! parent) {
            setTimeout(() => {
                toggle = true;
            }, navigator.userAgent.toLowerCase().includes('firefox') ? 200 : 50);
        }
    };

    const searchParentTransition = () => {
        let element = slot.parentElement;
        while (
            parent === undefined
            && element
            && document.body !== element
        ) {
            if (element.toggle !== undefined) {
                parent = element;
                parentObserver();
            } else {
                element = element.parentElement;
            }
        }
    };

    const parentObserver = () => {
        new MutationObserver((mutations) => {
            for (let mutation of mutations) {
                toggle = mutation.target.toggle;
            }
        }).observe(parent, {
            attributes: true,
            attributeFilter: [ 'class' ]
        });
    };

    const initTransition = () => {
        slot.toggle = toggle;

        if (toggle) {
            slot.classList.value = slotClasses + ' ' + transitions + ' ' + outTransition + ' ' + onState;
            transitionEndListener();
        } else {
            slot.hidden = ! offVisible;
            slot.classList.value = slotClasses + ' ' + inState;
            setTimeout(() => {
                slot.classList.value = slot.classList.value + ' ' + transitions + ' ' + inTransition;
                transitionEndListener();
            }, 250);
        }
    };

    const transitionEndListener = () => {
        slot.addEventListener('transitionend', (event) => {
            if (
                (event.target.toggle !== undefined)
                && (inTransition === '' || event.target === slot)
                && ((toggle && state === STATE.ENTERING) || (! toggle && state === STATE.LEAVING))
            ) {
                state = STATE.IDLE;
                if (! toggle) {
                    slot.hidden = ! offVisible;
                    slot.classList.value = slotClasses + ' ' + transitions + ' ' + inTransition + ' ' + inState;
                }
            }
        });
    };

    let initialized = false;
    let firstToggleState = toggle;
    $: firstToggleState !== toggle && (initialized = true);
    $: initialized && event(toggle);

    const event = (toggle) => {
        slot.toggle = toggle;
        toggle ? enterEvent() : leaveEvent();
    };

    const enterEvent = () => {
        if (slot.hidden) {
            slot.hidden = false;
            setTimeout(() => {
                enterEvent();
            }, 50);
        } else {
            state = STATE.ENTERING;
            slot.classList.value = slotClasses + ' ' + transitions + ' ' + inTransition + ' ' + onState;
        }
    };

    const leaveEvent = () => {
        state = STATE.LEAVING;
        slot.classList.value = slotClasses + ' ' + transitions + ' ' + outTransition + ' ' + outState;
    };
</script>

<div
    bind:this={div}
    hidden
/>
<slot />
