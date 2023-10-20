<template>
  p {{ arrLength }}
  <div class="hello">      
    <div class="intersFirst" />
    <div class="block" v-for="(el, i) in arrLength" :key="el" :id="el"></div>
    <div class="intersLast" />
  </div>
</template>

<script setup>
import { onMounted, onUpdated, ref, watch, nextTick } from "vue";
const startArr = [...Array(30).keys()];
const offset = 110
const arrLength = ref(startArr);
let once = false
let observer;

onMounted(() => {
  init();
});

const initInters = (targetEl, callback) => {
  let options = {
    root: document.querySelector(".hello"),
    rootMargin: "1px",
    threshold: 1.0,
  };

  observer = new IntersectionObserver(callback, options);
  observer.observe(targetEl);
};

const init = () => {
  const firstEl = document.querySelector(".intersFirst");
  const lastEl = document.querySelector(".intersLast");

  const callback = (entries, f,s,g) => {
    entries.forEach(async (entry) => {
      if (entry.isIntersecting) {
        const mock = (timeout = 2000) => {
          return new Promise((resolve) => {
            setTimeout(() => {
              const classEl = entry.target.getAttribute("class")
              switch (classEl) {
                 case 'intersFirst':
                  arrLength.value.reverse()
                  arrLength.value.push(arrLength.value.at(-1)-1, arrLength.value.at(-1)-2)
                  arrLength.value.reverse()
                  arrLength.value.splice(arrLength.value.length-2, 2);
                  document.querySelector(".hello").scrollLeft = document.querySelector(".hello").scrollLeft + offset
                  break
                case 'intersLast':
                  arrLength.value.push(arrLength.value.at(-1)+1, arrLength.value.at(-1)+2);
                  arrLength.value.splice(0,2);
                  document.querySelector(".hello").scrollLeft = document.querySelector(".hello").scrollLeft - offset

                  if(!once) {
                    once = true
                    initInters(firstEl, callback);
                  }
                  break;
              }
              resolve()
            }, timeout);
          });
        }
              
        try {
          await mock()
        } catch(e) {
          console.log('err', e)
        }
      }
    });
  };

  initInters(lastEl, callback);
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
