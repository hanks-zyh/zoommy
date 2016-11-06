<template>
<div id="app" class="main">
  <div class="header">
    <input class="input-search" v-model="keyword" @keyup.enter="search">
    <input class="btn-search" type="button" @click="search" name="search" value="搜索">
  </div>
  <div class="main masonry">
    <figure v-for="img in imgs">
      <img :src="img.base64" alt>
    </figure>
  </div>
  <div class="loadMore" v-show="imgs.length > 29" @click="loadMore">
    加载更多...
  </div>
</div>

</template>
<script>
export default {
  data() {
    return {
      imgs: [],
      page: 0,
      loading: false,
      keyword: '',
    }
  },
  created() {

  },
  methods: {
    loadMore(){
      this.search();
    },
    search() {

      if(this.loading){
        return
      }
      var that = this
      var p = 0
      if (this.page) {
        p = this.page
      }
      var q = ''
      if (this.keyword) {
        q = this.keyword
      }

      var url = `http://104.194.66.230:3000/zoommy?q=${q}&p=${p}`

      this.loading = true
      fetch(url)
        .then((response) => response.json())
        .then((json) => {
          console.log(json)
          this.page += 1
          for (var i = 0; i < json.items.length; i++) {
            var item = json.items[i]
            var img = {}
            img.extension = item.extension
            img.base64 = `data:image/${item.extension};base64,${item.base64}`
            img.src = item.preview_url
            img.width = item.preview_width
            img.height = item.preview_height
            while (img.width > 370 || img.height > 400) {
              img.width = img.width / 2
              img.height = img.height / 2
            }
            img.primaryColor = item.primary_color
            img.hdSrc = item.full_url
            img.title = item.description
            img.content = item.description
            img.style1 = 'flex-grow:' + img.width * 100 / img.height + ';flex-basis:' + img.width * 240 / img.height + 'px;'
            img.style2 = 'padding-bottom:' + img.height / img.width * 100 + '%'
            that.imgs.push(img)
          }
          this.loading = false
        })
        .catch((error) => {
          console.error(error);
        });
    }
  }
}

</script>

<style>
* {
  margin: 0;
  padding: 0;
}
html{
  height: 100%;
}

body {
  height: 100%;
  margin: 0;
  overflow-x: hidden;
  font-size: 12px;
  -webkit-font-smoothing: antialiased;
  font-family: "Microsoft YaHei", arial, sans-serif;
  min-height: 512px;
  min-width: 1000px;
  -moz-osx-font-smoothing: grayscale;
  background-color: #253238;
  overflow-y: auto;

}

.masonry {
  column-width: 250px;
  column-gap: 15px;
  width: 90%;
  max-width: 1100px;
  margin: 50px auto;
  margin-top: 2em;
}

.masonry figure {
  margin: 0;
  padding: 0;
  padding-bottom: 0;
  transition: opacity .4s ease-in-out;
  column-break-inside: avoid;
}

.masonry figure img {
  width: 100%;
  height: auto;
  margin-bottom: 15px;
}

.masonry:hover figure:not(:hover) {
  opacity: 0.4;
}

.img-wrapper {
  width: 33.3%;
  transition: .8s opacity;
}

.masonry:hover .img-wrapper {
  opacity: 0.3;
}

.masonry:hover .img-wrapper:hover {
  opacity: 1;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.header {
  width: 800px;
  display: flex;
  justify-content: center;
  margin-top: 2em;
  margin-bottom: 2em;
  height: 4em;
}

.main {
  margin: 0 auto;
  width: 800px;
}

.input-search {
  padding: 0.5em;
  background: #fff;
  border: none;
  color: #333;
  float: left;
  font-size: 16px;
  outline: none;
  width: 506px;
  flex: 1;
}

.btn-search {
  background: #19b955;
  color: #fff;
  -webkit-appearance: none;
  border: 0;
  cursor: pointer;
  font-size: 18px;
  left: 530px;
  outline: none;
  width: 110px;
}


.cover {
  position: absolute;
  vertical-align: bottom;
  top: 0;
  background-color: rgba(0, 0, 0, 0.6);
  width: 100%;
  height: 100%;
  object-fit: cover;
  color: white;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  cursor: pointer;
  opacity: 0;
  transition: opacity 0.3s;
}

.cover:hover {
  opacity: 1;
}

.cover>h3 {
  margin: 10px;
}

.cover>p {
  margin: 10px;
  margin-bottom: 20px;
  line-height: 20px;
  font-size: 12px;
}

.loadMore{
  display: flex;
  height: 5em;
  justify-content: center;
  align-items: center;
  font-size: 1.5em;
  color: #9a9a9a;
  cursor: pointer;
}
</style>
