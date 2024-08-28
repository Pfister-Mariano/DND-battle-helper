<template>
    <section class="overlay">
        <article class="overlayContent">
            <div class="overlayClose" @click="$emit('close')"><i class="fa-solid fa-xmark"></i></div>
            <slot></slot>
        </article>
        <article class="overlayBackground" @click="$emit('close')"></article>
    </section>
</template>

<script setup>

</script>

<style lang="scss" scoped>
@import '../styles/variables.scss';

section.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 175px;
    overflow: hidden;
    z-index: 100;

    article.overlayBackground {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(51, 65, 85, 0.5);
        z-index: 2;
    }

    article.overlayContent {
        position: relative;
        height: 100%;
        width: 100%;
        max-width: 1500px;
        /*height: 1000px;*/
        z-index: 3;
        background-color: $color-blue-25;
        border-radius: 25px;
        padding: 3rem;
    }

    .overlayClose {
        position: absolute;
        font-size: 3rem;
        text-align: center;

        right: 1.5rem;
        top: 1.5rem;

        border-radius: 100%;
        color: $color-blue-dark;
        z-index: 10;
        transition: all ease-in-out 0.2s;
        cursor: pointer;

        &:hover {
            color: $color-blue-highlight;
        }
    }
}
</style>