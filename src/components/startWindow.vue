<template lang="pug">
.start.abs.flex-column.cap(ref="startWindow")
    h1.special-font memory game
    button.btn.cap.flex.transition(type="button", @click="start") start
    .get-name(ref="getName")
        label.cap(for="name") what is your name?
        input.name.cap(id="name", placeholder="your name", ref="nameInput")
        button.cap(type="button", @click="go") go
</template>
<script>
export default {
    methods: {
        start() {
            let nameInput = this.$refs.nameInput;
            this.$refs.getName.classList.add("show");
            nameInput.focus();
        },
        go() {
            let nameSpan = document.querySelector(".header .name"),
                nameInput = this.$refs.nameInput;
            this.$emit("sound", 'begain');
            this.$refs.startWindow.classList.add("hidden");
            nameSpan.textContent = !nameInput.value ? "unknown" : nameInput.value;
            this.$emit("timer");
        },
    },
}
</script>
