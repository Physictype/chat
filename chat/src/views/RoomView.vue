<script setup lang="ts">
import { onMounted, ref} from 'vue';
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyBysw4Xti0Jet9qXyWqtzgmAh5ThzE-Sgw",
  authDomain: "chat-servre.firebaseapp.com",
  projectId: "chat-servre",
  storageBucket: "chat-servre.appspot.com",
  messagingSenderId: "545009573060",
  appId: "1:545009573060:web:d4733150f5e8fbf1983d91",
  measurementId: "G-2EWXW1KMGM"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);

import { getFirestore, doc, onSnapshot } from "firebase/firestore";

const db = getFirestore(app);


let input = ref(null);
let markdowned = ref(null);

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
let messages = ref([])
const messageListener = onSnapshot(doc(db, "rooms", "test"), (doc) => {
    messages = doc.data().messages;
});

function updateMarkdown() {
    document.getElementById("markdowned").innerHTML = "";
    document.getElementById("markdowned")?.appendChild(getMarkdown(input.value.value));
}

onMounted(() => {
    input.value = document.getElementById("input");
    markdowned.value = document.getElementById("markdowned");
    input.value.addEventListener('input',updateMarkdown);
})

</script>
<template>
    <div id="messagesContainer">
        <Message v-for="message in messages" :author="message.author" :message="message.message"/>
    </div>
    <div id="markdowned" style="color: white"></div>
    <textarea id="input"/>
</template>
<style>
#input {
    position: absolute;
    display: block;
    width: 95%;
    top: 95%;
    background-color: #666666;
}
#markdowned {
    position: absolute;
    display: block;
    width: 95%;
    bottom: 5%;
    
}
</style>