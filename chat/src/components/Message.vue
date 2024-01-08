<script setup lang='ts'>
let props = defineProps(['author','message']);
function getMarkdown(str) {
    let container = document.createElement('div');
    let asterisks = 0
    let openedAsterisk = false;
    let slashed = false;
    let headingnum = 0;
    for (let i = 0; i < str.length; i++) {
        let c = str[i];
        if (c == '*' && !slashed) {
            if (openedAsterisk) {
                asterisks --;
                if (asterisks == 0) {
                    openedAsterisk = false;
                }
            } else {
                asterisks ++;
            }
        } else if (c == '#' && !slashed) {
            headingnum += 1;
        } else if (c == '\\') {
            slashed = true;
        } else {
            if (asterisks > 0) {
                openedAsterisk = true;
            }
            let s = document.createElement('span');
            let curr = s;
            if (headingnum == 1) {
                let temp = document.createElement('h1');
                curr.appendChild(temp);
                curr = temp;
            }
            if (asterisks == 0) {
                console.log("a")
                curr.innerText = c;
                container.appendChild(s);
                continue;
            }
            if (asterisks % 2 == 1) {
                let temp = document.createElement("em");
                curr.appendChild(temp);
                curr = temp;
            }
            if (asterisks % 4 >= 2) {
                let temp = document.createElement("strong");
                curr.appendChild(temp);
                curr = temp;
            }
            curr.innerText = c;
            container.appendChild(s);
        }
    }
    return container;
}
onMounted(() => {
    
})
</script>
<template>
    <div class="message">
        {{ author }}:
    </div>
</template>
<style>
</style>