<template>
  p {{ arrLength }}
  <div class="hello">      
    <div class="intersFirst">1</div>
    <div class="block" v-for="(el, i) in arrLength" :key="el" :id="el">

      <div class="intersLast" v-if="i === arrLength.length - 1">
        {{ arrLength.length }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, onUpdated, ref, watch, nextTick } from "vue";
const xyu = [...Array(30).keys()];
let arrLength = ref(xyu);
let observer;

const initInters = (targetEl, callback) => {
  let options = {
    root: document.querySelector(".hello"),
    rootMargin: "0px",
    threshold: 1.0,
  };

  observer = new IntersectionObserver(callback, options);
  observer.observe(targetEl);
};

onMounted(() => {
  init();
});

onUpdated(() => {
})

const init = () => {
  const firstEl = document.querySelector(".intersFirst");

  const callbackForFirst = (entries, observer) => {
    console.log('callbackForFirst', entries);
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        arrLength.value.unshift(arrLength.value.at(1)-1, arrLength.value.at(-1)-2, arrLength.value.at(-1)-3);
        arrLength.value.splice(arrLength.length-1,1);
        nextTick(() => {
          init();
        });
      }
    });
  };

  const lastEl = document.querySelector(".intersLast");

  const callbackForlast = (entries, observer) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        observer.unobserve(lastEl);
        arrLength.value.push(arrLength.value.at(-1)+1, arrLength.value.at(-1)+2);
        arrLength.value.splice(0,1);
        nextTick(() => {
          init();
        });
      }
    });
  };

  initInters(lastEl, callbackForlast);
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  box-sizing: border-box;
}
.hello {
  display: flex;
  overflow-x: scroll;
  position: relative;
  padding-left: 20px;
}
.block {
  min-width: 100px;
  width: 100px;
  height: 100px;
  border: 1px red solid;
}
</style>
