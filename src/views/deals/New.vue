<template>
  <div class="container">
    <div class="columns">
      <div class="column is-2 ">
        <left-bar></left-bar>
      </div>
      <div class="column is-10">
        <nav class="breadcrumb" aria-label="breadcrumbs">
          <ul>
            <li><router-link to="/">Mindeal</router-link></li>
            <li class="is-active"><a href="#" aria-current="page">添加新折扣</a></li>
          </ul>
        </nav>
        <section>
          <editor :deal="deal" editor-title="Add New Deal" editor-submit="Submit" @deal-saved="save"></editor>
          <b-loading :is-full-page="false" :active.sync="isLoading"></b-loading>
        </section>
      </div>
    </div>
  </div>
</template>

<script>
    import LeftBar from "./LeftBar"
    import Editor from "./DealEditor"

    export default {
        data() {
            return {
                deal: {
                    title: '',
                    url: '',
                    picture: '',
                    category: '',
                    status: 'draft',
                    tags: [],
                    msrp: '',
                    price: '',
                    coupon: '',
                    body: ''
                },
                isLoading: false,
            }
        },
        components: {LeftBar, Editor},
        methods: {
            save(deal) {
                this.isLoading = true;
                const api = this.$store.state.api.url;
                const tmp = document.createElement('DIV');
                tmp.innerHTML = deal.body;
                deal.txt = tmp.textContent || tmp.innerText || '';
                this.axios.post(api + '/deals', deal).then(() => {
                    this.deal = {
                        title: '',
                        url: '',
                        picture: '',
                        category: '',
                        status: 'draft',
                        tags: [],
                        msrp: '',
                        price: '',
                        coupon: '',
                        body: ''
                    };
                    this.$toast.open({
                        duration: 5000,
                        message: 'Deal已保存',
                        type: 'is-success'
                    });
                    this.$router.push({name: 'deal-list'});
                })
            }
        }
    }
</script>
