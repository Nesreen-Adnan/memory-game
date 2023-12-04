<template lang="pug">
.stop.flex-column.cap(ref="stopWindow")
    p.special-font game over! 
    p.wrongs.p-10(ref="wrong")
    button.btn.cap(type="button", @click="$emit('again')") play again
</template>
<script>
export default {
    methods: {
        stop() {
            let wrongs = document.querySelector(".header .wrong-tries .wrongs"),
                wrongs2 = this.$refs.wrong;
            if (wrongs.textContent > 1) {
                wrongs2.textContent = `you have ${wrongs.textContent} wrong tries`;
            } else if (wrongs.textContent == 1) {
                wrongs2.textContent = "you have one wrong try";
            } else {
                wrongs2.textContent = "you don't have any wrong try";
            }
            this.$emit("sound", "stop");
            this.$refs.stopWindow.classList.add("show");
        },
    }
}
</script>