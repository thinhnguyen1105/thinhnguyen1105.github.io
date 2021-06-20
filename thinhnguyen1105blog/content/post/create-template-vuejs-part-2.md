---
title: "Tối ưu hoá hệ thống VueJS (Phần 2)"
date: 2021-06-20T12:30:00+07:00
description: "Cuối cùng mình cũng tiếp tục viết series này sau một thời gian dài bận sấp mặt 😂. Quả thực là công việc viết blog"
image: "/image/posts/vuejs/vuejs-part2.jpg"
tags: ["Development", "Front-end", "VueJS"]
---

# Tối ưu hệ thống VueJS (Phần 2)

Cuối cùng mình cũng tiếp tục viết series này sau một thời gian dài bận sấp mặt 😂. Quả thực là công việc viết blog với một lập trình viên đòi hỏi sự sắp xếp thời gian vô cùng hợp lí và quan trọng nhất là vượt qua sự "lười" sau khi đi làm về và cần có một năng lượng muốn chia sẻ kiến thức với mọi người nữa =))) . Cảm ơn những lời động viên đã giúp mình có động lực viết tiếp những dòng này. Stay tuned 💪🏻

Quay trở lại với kiến trúc của VueJS như đã nói ở bài trước, mình đã mô tả cách xây dựng store VueX, cách tạo repository theo factory để gọi API một cách "siêu tiện" và cách xây dựng global component để có thể dùng được ở khắp mọi nơi trong project. Kỳ này mình sẽ nói đến cách xây dựng plugin cho vue và các practice code với VueJS để có thể dev nhanh trong "một nốt nhạc" nhé =))

## 1. Xây dựng plugin cho Vue

Nhìn chung plugin là một tool sở hữu 1 chức năng mà mình tự tạo ra trong project để có thể dùng ở bất kì đâu chỉ bằng 1 câu lệnh. Ví dụ thế này nhá, khi bạn muốn gọi một thông báo chứa message là 'Cập nhật thành công' và hiển thị cho người dùng. Thay vì mình phải gọi ra một cái thông báo màu xanh → gán message cho nó → gọi nó để chạy và hiển thị cho người dùng → đóng thông báo, thì mình sẽ viết hết vào 1 plugin và gọi ra như này. So easy 🤟🏻

```jsx
this.$message.success("Cập nhật thành công");
```

Vậy để tạo plugin thì mình cần làm như nào ?

### Bước 1: Chuẩn bị UI component

Tất nhiên là vẫn cần chuẩn bị cái hiển thị cho người dùng rùi

mình sẽ tạo một component BaseMessage.vue như thế này nhé, mình dùng thư viện vuetify để có v-snackbar và điều khiển các trạng thái và màu sắc của component này qua biến message

```jsx
<template>
  <v-snackbar
    v-model="message.isDisplay"
    :color="message.color"
    :timeout="message.timeout ? message.timeout : 3000"
  >
    <div class="d-inline-flex pa-0">

      <span
        :class="
          `${message.color === 'white' ? 'primary--text' : 'white--text'}`
        "
        >{{ message.text }}</span
      >
    </div>
  </v-snackbar>
</template>
<script>
export default {
  data() {
    return {
      message: this.$message.messageData
    }
  },
  methods: {
    close() {
      this.$message.close()
    }
  }
}
</script>
```

Ok vậy là xong phần hiển thị cho người dùng, giờ mình sẽ đi tiếp đến phần tạo plugin. Plugin mình sẽ viết bằng javascript và ở trong folder plugins cho gọn code nhé

```jsx
// gọi component BaseMesssage để hiển thị
import BaseMessage from "@/global/BaseMessage.vue";
// khai báo cấu trúc message
const message = {
  messageData: {
    isDisplay: false,
    text: "",
    color: "error",
  },
  // hàm đóng message
  close() {
    Object.assign(this.messageData, { isDisplay: false, text: "" });
  },
  // hàm hiển thị message theo màu sắc và chữ mình mong muốn
  show(color, text, timeout) {
    Object.assign(this.messageData, { isDisplay: true, text, color, timeout });
  },
  error(msg, timeout) {
    this.show("error", msg, timeout);
  },
  success(msg) {
    this.show("success", msg);
  },
  warning(msg) {
    this.show("warning", msg);
  },
  loading(msg, timeout) {
    this.show("white", msg, timeout);
  },
};
// export cho plugin để có thể cắm vào main.js để thành plugin
export default {
  install(Vue, pluginName = "$message") {
    Vue.prototype[pluginName] = message;
    Vue.component("plugin-message", BaseMessage);
  },
};

export { message };
```

Sau khi đã có plugin thì mình sẽ cắm nó vào main.js để có thể gọi this.$message ở bất kì đâu trong project chạy js

```jsx
// pluginHelper.js - mục đích là để import plugin nhanh hơn
export default {
  create(plugins) {
    return {
      install(Vue) {
        Object.entries(plugins).forEach(([pluginName, plugin]) => {
          if (plugin.install && typeof plugin.install === "function") {
            Vue.use(plugin, pluginName);
          } else {
            Vue.prototype[pluginName] = plugin;
          }
        });
      },
    };
  },
};
```

```jsx
// main.js
import Vue from "vue";
import message from "@/plugins/message.js";
import pluginHelper from "@/helpers/pluginHelper.js";

// vậy là từ nay lúc nào tạo plugin gì chỉ cần nhét plugin vào pluginHelper.create là xong
Vue.use(
  pluginHelper.create({
    $message: message,
  })
);
```

## 2. Cách code siêu nhanh với VueJS

Mình có thấy các bạn dev front-end, khi đã code quen đến một mức độ nhất định thì sẽ code rất nhanh. Thậm chí không cần nghĩ vẫn có thể code được. Thực tế là mình nhận thấy các bạn đang viết lại một cái template của một framework hoặc một project mà cấu trúc của file đấy các bạn đã viết đến 1000 lần. Là một lập trình viên, mình luôn nghĩ đến cách tối hưu hiệu suất làm việc, nói trắng ra là code ít nhưng hiệu quả cao =)))) làm ít mà ăn nhiều. Cảm ơn vscode với snippet thần thánh có thể customize được và các bạn có thể tạo template chỉ bằng 1 "chữ"

Ví dụ: khi mình code file repository để sinh ra API CRUD hay code store Vuex cho những API ấy thì thay vì viết từng dòng hay copy từ file khác sang sửa thì tạo 1 snippet sẽ giúp bạn tối ưu thời gian hơn rất nhiều. Và trông ngầu hơn nữa 😎

Ok giờ đi tạo snippet nhé !!

Trong Vscode các bạn gõ crlt + shift + p hoặc command + shift + p với macos

![CodeDemo](/image/posts/vuejs/code-demo.png)

Chọn vào configure user snippets

Chọn ngôn ngữ mà bạn muốn viết snippet → vscode sẽ tự gợi ý khi ở trong file viết bằng ngôn ngữ đấy. Ở đây mình chọn javascript. Sau khi chọn các bạn sẽ thấy nó mở ra 1 file json có dạng như này, mình sẽ uncomment phần dưới example

```jsx
{
  // Place your snippets for bat here. Each snippet is defined under a snippet name and has a prefix, body and
  // description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
  // $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the
  // same ids are connected.
  // Example:
"Print to console": {
    "prefix": "log",
    "body": [
      "console.log('$1');",
      "$2"
    ],
    "description": "Log output to console"
  }
}
```

Sau khi save file thì sang một file js nào đó gõ thử log và enter thì nó sẽ tự ra console.log. Yeah, vậy chúng ta có thể tự tạo 1 template và gõ 1 lệnh là ra rồi

Đây là file store mình đã custom cho template vue của mình để viết store nhanh hơn. Với $1, $2 là những điểm con trỏ dừng lại để mình có thể điền tên của đối tượng mà mình đang viết. \t là tab thụt vào dòng, và $0 là bỏ trống dòng đó

```jsx
{
  "Vue template store ": {
    "prefix": "storevue",
    "body": [
      "import { RepositoryFactory } from '@/api/factory/repositoryFactory'",
      "const $1 = RepositoryFactory.get('$1')",
      "$0",
      "const state = {",
      "\t$1s: [],",
      "\t$1: {},",
      "};",
      "$0",
      "const actions = {",
      "\tasync fetch({ commit }, params = {}) {",
      "\t\tconst res = await $1.fetch({",
      "\t\t\t...params,",
      "\t\t});",
      "\t\tcommit('set$1s', res.data);",
      "\t\treturn res.data;",
      "},",
      "async fetchOne({ commit }, id) {",
      "const res = await $1.fetchOne(id);",
      "commit('setOne$1', res.data);",
      "return res.data",
      "},",
      "async create(_, data) {",
      "const res = await $1.create(data);",
      "return res.data",
      "},",
      "async delete({ commit }, id) {",
      "await $1.remove(id);",
      "return commit('remove$1', id);",
      "},",
      "async update(_, { id, ...data }) {",
      "const res = await $1.update(id, data);",
      "return res.data",
      "},",
      "};",
      "$0",
      "const mutations = {",
      "set$1s(state, data) {",
      "return state.$1s = data;",
      "},",
      "remove$1(state, id) {",
      "state.$1s = state.$1s.filter(val => val.id !== id)",
      "},",
      "setOne$1(state, data) {",
      "state.$1 = data",
      "}",
      "};",
      "$0",
      "const getters = {",
      "getAll: (state) => {",
      "return state.$1s;",
      "},",
      "getOne: (state) => {",
      "return state.$1;",
      "},",
      "};",
      "export default {",
      "namespaced: true,",
      "state,",
      "actions,",
      "mutations,",
      "getters,",
      "}",
    ],
    "description": "Template create vuex store"
  },
}
```

Giờ thì tận hưởng thành quả nào =)))))))))) 😁😁😁😁😁

![CodeDemo](/image/posts/vuejs/code-demo-1.png)

Sau khi lưu xong là tự có cái gợi ý storevue khi gõ chữ store nhé =))). Và tiếp theo là điều kì diệu

![CodeDemo](/image/posts/vuejs/code-demo-2.png)

![CodeDemo](/image/posts/vuejs/code-demo-3.png)

Sau khi hiện template thì có tất cả các con trỏ, mình chỉ cần điền tên cho đối tượng mình muốn tạo store là xong. Bravo !! hi vọng những cách này sẽ giúp bạn code nhanh hơn trên con đường làm lập trình viên của bạn. 🔥🔥🔥🔥

