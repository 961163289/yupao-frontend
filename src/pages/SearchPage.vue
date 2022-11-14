<template>
  <form action="/">
    <van-search
        v-model="searchText"
        show-action
        placeholder="请输入要搜索的标签"
        @search="onSearch"
        @cancel="onCancel"
    />
  </form>

  <van-divider content-position="left">已选标签</van-divider>
  <div v-if="activeIds.length === 0">请选择标签</div>
  <van-row gutter="16" style="padding: 0 16px">
    <van-col v-for="tag in activeIds">
      <van-tag closeable size="small" type="primary" @close="doClose(tag)">
        {{ tag }}
      </van-tag>
    </van-col>
  </van-row>
  <van-divider content-position="left">选择标签</van-divider>
  <van-tree-select
      v-model:active-id="activeIds"
      v-model:main-active-index="activeIndex"
      :items="tagList"
  />
  <div style="padding: 12px">
    <van-button block type="primary" @click="doSearchResult">搜索</van-button>
  </div>

</template>

<script setup>
import {ref} from 'vue';
import {useRouter} from "vue-router/dist/vue-router";

const router = useRouter();
const searchText = ref('');

const originTagList = [
  {
    text: '性别',
    children: [
      {text: '男', id: '男'},
      {text: '女', id: '女'},
    ],
  },
  {
    text: '年级',
    children: [
      {text: '大一', id: '大一'},
      {text: '大二', id: '大二'},
      {text: '大三', id: '大三'},
      {text: '大四', id: '大四'},
      {text: '研一', id: '研一'},
      {text: '研二', id: '研二'},
    ],
  }
]

// 标签列表
let tagList = ref(originTagList);

/**
 * 搜索过滤
 * @param val
 */
const onSearch = (val) => {
  // filter 过滤: 仅保留包含了搜索词的标签
  tagList.value = originTagList.map(parentTag => {
    //ES6语法: 将原数组中内容取出放到一个新的数组中
    const tempChildren = [...parentTag.children];
    const tempParentTags = {...parentTag};
    tempParentTags.children = tempChildren.filter(item => item.text.includes(searchText.value));
    return tempParentTags;
  })
};

const onCancel = () => {
  searchText.value = '';
  tagList.value = originTagList;
};

//已选中的标签
const activeIds = ref([]);
const activeIndex = ref(0);


// 移除标签
const doClose = (tag) => {
  //过滤掉要移除的标签  返回true,表示要保留的标签
  activeIds.value = activeIds.value.filter(items => {
    return items !== tag;
  })
}


const doSearchResult = () => {
  router.push({
    path: '/user/list',
    query: {
      tags: activeIds.value
    }
  })
};

</script>

<style scoped>

</style>