<template>
     <div class="container-fluid mt-5">
        <div class="row">
            <main role="main" class="col-lg-12 ml-auto mr-auto" style="maxWidth: '500px'">
                <div class="content mr-auto ml-auto">
                    <div class="card mb-4" v-bind:key="index" v-for="(item, index) in posts">
                        <div class="card-header">
                            <img
                                class='mr-2'
                                width='30'
                                height='30'
                                createPost src=""
                            />
                            <small class="text-muted">{{item.author}}</small>
                        </div>
                        <ul id="postList" class="list-group list-group-flush">
                            <li class="list-group-item">
                                <p>{{item.content}}</p>
                            </li>
                            <li key={key} class="list-group-item py-2">
                                <small class="float-left mt-1 text-muted">
                                    <!-- TIPS: {window.web3.utils.fromWei(post.tipAmount.toString(), 'Ether')} ETH -->
                                </small>
                                <button
                                    class="btn btn-link btn-sm float-right pt-0"
                                    :name="item.id"
                                    v-on:click="tipPost(event.target.name, tipAmount)">
                                    TIP 0.1 ETH
                                </button>
                            </li>
                        </ul>
                    </div>
                </div>

                <form v-on:submit.prevent="createPost(content)" >
                    <div class="form-group mr-sm-2">
                        <input
                            id="postContent"
                            type="text"
                            v-model="content"
                            class="form-control"
                            placeholder="What's on your mind?"
                            required />
                    </div>
                    <button type="submit" class="btn btn-primary btn-block">Share</button>
                </form>
            </main>
        </div>
    </div>
</template>

<script>
// import Identicon from 'identicon.js';

export default {
  props: {
    posts: Array,
    createPost: Function,
    tipPost: Function
  },
  data() {
      return {
        tipAmount: '',
        content: '' 
      }
  },
  mount() {
      this.tipAmount = window.web3.utils.toWei('0.1', 'Ether')
  }
}
</script>

