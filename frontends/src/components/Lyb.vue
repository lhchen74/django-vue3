<template>
  <div class="row">
    <div class="column">
      <table>
        <thead>
          <tr>
            <th>标题</th>
            <th>作者</th>
            <th>内容</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in ly_list" :key="item.url">
            <td>{{ item.title }}</td>
            <td>{{ item.author }}</td>
            <td>{{ item.content }}</td>
            <td>
              <button @click="editLyb(item)" style="margin: 0.5rem">
                编辑
              </button>
              <button @click="deleteLyb(item)">删除</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="column">
      <input type="hidden" v-model="lyb.url" />
      <div>
        <label for="title">标题</label
        ><input type="text" id="title" v-model="lyb.title" />
      </div>
      <div>
        <label for="author">作者</label
        ><input type="text" id="author" v-model="lyb.author" />
      </div>
      <div>
        <label for="content">内容</label
        ><textarea id="content" v-model="lyb.content"></textarea>
      </div>
      <button @click="saveLyb">确定</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { reactive, onMounted, toRefs } from "vue";

export default {
  name: "Lyb",
  setup() {
    let base_url = "http://localhost:8000/api/lyb/";

    const lyb_blank = { url: "", title: "", author: "", content: "" };

    const state = reactive({
      ly_list: [],
      lyb: Object.assign({}, lyb_blank),
    });

    const getLyb = () => {
      axios
        .get(base_url)
        .then((res) => {
          state.ly_list = res.data;
          state.lyb = Object.assign({}, lyb_blank);
        })
        .catch((err) => {
          console.log(err);
        });
    };

    const editLyb = (item) => {
      state.lyb.url = item.url;
      state.lyb.title = item.title;
      state.lyb.author = item.author;
      state.lyb.content = item.content;
    };

    const deleteLyb = (item) => {
      axios
        .delete(item.url)
        .then(() => {
          getLyb();
        })
        .catch((err) => {
          console.log(err);
        });
    };

    const saveLyb = () => {
      let newLyb = {
        title: state.lyb.title,
        author: state.lyb.author,
        content: state.lyb.content,
      };

      if (!state.lyb.url) {
        // add
        axios
          .post(base_url, newLyb)
          .then(() => {
            getLyb();
          })
          .catch((err) => {
            console.log(err);
          });
      } else {
        // update
        axios
          .put(state.lyb.url, newLyb)
          .then(() => {
            getLyb();
          })
          .catch((err) => {
            console.log(err);
          });
      }
    };

    onMounted(() => {
      getLyb();
    });

    return {
      ...toRefs(state),
      editLyb,
      saveLyb,
      deleteLyb,
    };
  },
};
</script>
