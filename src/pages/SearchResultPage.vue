<template>
  <user-card-list :user-list="userList"/>
  <van-empty v-if="!userList || userList.length < 1" description="搜索结果为空"></van-empty>
</template>

<script setup>
import {onMounted, ref} from 'vue';
import {useRoute} from "vue-router";
import myAxios from "@/plugins/myAxios";
import {Toast} from "vant";
import qs from "qs";
import UserCardList from "@/components/UserCardList.vue";

const route = useRoute();
const {tags} = route.query;

const userList = ref([]);

onMounted(async () => {
  const userListData = await myAxios.get('/user/search/tags', {
    params: {
      tagNameList: tags
    },
    paramsSerializer: {
      serialize: function (params) {
        return qs.stringify(params, {arrayFormat: 'repeat'})
      }
    }
  })

      .then(function (response) {
        console.log('/user/search/tags succeed' + response);
        Toast.success('请求成功');
        return response?.data;
      })

      .catch(function (error) {
        console.log('/user/search/tags error', error);
        Toast.fail('请求失败');
      })

  if (userListData) {
    userListData.forEach(user => {
      if (user.tags) {
        user.tags = JSON.parse(user.tags);
      }
    })
    userList.value = userListData;
  }

})


</script>

<style scoped>

</style>