---
title: Vue.js - 表單驗證實作
date: 2017-09-10 21:06:07
categories: "Vue.js"
tags: ["JavaScript", "Vue.js"]
---
# 前言
最近覺得Vue還蠻好玩的，剛好我需要挑一個前端框架來玩玩，所以就挑這個囉！

# 實作說明
## 整體流程
我要用Vue來監控Form所有的值，使用Regular Expression(正規表達式)來驗證是否錯誤，錯誤提示的部分使用Bootstrap 4
## 簡單範例
HTML部分建立一個input
數值使用v-model綁在data上，v-bind:class則是顯示錯誤的開關
在建立一個invalid-feedback的div，裡面用來放錯誤訊息
``` html
<label for="username">帳號</label>
<input type="text" class="form-control" name="username" v-bind:class="{ 'is-invalid': usernameError }" v-model="username"
    placeholder="Username">(最多10個字)
<div class="invalid-feedback">
    {{ userErrMsg }}
</div>
```
Vue部分
創建3個Data
username用來繫結輸入的值、usernameError用來控制是否錯誤、userErrMsg用來控制錯誤訊息
使用Vue的watch事件用正規表達式檢查是否錯誤
``` js
var app = new Vue({
    el: '#app',
    data: {
        username: '',
        usernameError: false,
        userErrMsg: ''
    },
    watch: {
        username: function () {
            var isText = /^[a-zA-Z0-9]+$/;
            if (!isText.test(this.username)) {
                this.usernameError = true;
                this.userErrMsg = '請勿包含特殊字元';
            }
            else if (this.username.length > 10) {
                this.usernameError = true;
                this.userErrMsg = '請勿超過10個字';
            }
            else {
                this.usernameError = false;
            }
        }
    }
});
```

# 完整實作範例
HTML部分
``` html
<div id="app">
    <div class="row">
        <div class="form-group">
            <label for="username">帳號</label>
            <input type="text" class="form-control" name="username" v-bind:class="{ 'is-invalid': usernameError }" v-model="username"
                placeholder="Username">(最多10個字)
            <div class="invalid-feedback">
                {{ userErrMsg }}
            </div>
        </div>
    </div>
    <div class="row">
        <div class="form-group">
            <label for="">密碼</label>
            <input type="text" class="form-control" name="password" v-bind:class="{ 'is-invalid': passwordError }" v-model="password"
                placeholder="Password">(至少6位，小於15位，必須包含大小寫和數字)
            <div class="invalid-feedback">
                {{ passErrMsg }}
            </div>
        </div>
    </div>
    <div class="row">
        <div class="form-group">
            <label for="">E-Mail</label>
            <input type="text" class="form-control" name="email" v-bind:class="{ 'is-invalid': emailError }" v-model="email" placeholder="Email">
            <div class="invalid-feedback">
                {{ emailErrMsg }}
            </div>
        </div>
    </div>
</div>
```
JS部分
``` js
var app = new Vue({
    el: '#app',
    data: {
        username: '',
        usernameError: false,
        userErrMsg: '',
        password: '',
        passwordError: false,
        passErrMsg: '',
        email: '',
        emailError: false,
        emailErrMsg: ''
    },
    watch: {
        username: function () {
            var isText = /^[a-zA-Z0-9]+$/;
            if (!isText.test(this.username)) {
                this.usernameError = true;
                this.userErrMsg = '請勿包含特殊字元';
            }
            else if (this.username.length > 10) {
                this.usernameError = true;
                this.userErrMsg = '請勿超過10個字';
            }
            else {
                this.usernameError = false;
            }
        },
        password: function () {
            var isText = /^[a-zA-Z0-9]+$/;
            var inclde = /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,15}$/;

            if (!isText.test(this.password)) {
                this.passwordError = true;
                this.passErrMsg = '請勿包含特殊字元';
            }
            else if (this.password.length < 6) {
                this.passwordError = true;
                this.passErrMsg = '請勿少於6個字';
            }
            else if (this.password.length > 15) {
                this.passwordError = true;
                this.passErrMsg = '請勿超過15個字';
            }
            else if (!include.test(this.password)) {
                this.passwordError = true;
                this.passErrMsg = '至少包括一個大小寫字母或數字';
            }
            else {
                this.passwordError = false;
            }
        },
        email: function () {
            var isMail = /^\w+((-\w+)|(\.\w+))*\@[A-Za-z0-9]+((\.|-)[A-Za-z0-9]+)*\.[A-Za-z]+$/;
            if (!isMail.test(this.email)) {
                this.emailError = true;
                this.emailErrMsg = 'email格式錯誤';
            }
            else {
                this.emailError = false;
            }
        }
    }
});

```
# 後記
表單驗證使用Vue.js之後做起來方便多了，驗證的非常及時，跟Bootstrap搭配起來也非常方便，只要監控的數值改變，Vue.js便會重新渲染，相較自己寫JS按下Submit才檢查來得更即時。