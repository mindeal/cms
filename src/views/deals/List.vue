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
            <li class="is-active"><a href="#" aria-current="page">折扣列表</a></li>
          </ul>
        </nav>
        <section>
          <b-field>

          </b-field>
          <b-field>
            <b-radio-button v-model="status" native-value="published" type="is-success">
              <span>已发布</span>
            </b-radio-button>

            <b-radio-button v-model="status" native-value="draft" type="is-warning">
              <span>草稿</span>
            </b-radio-button>
          </b-field>
        </section>
        <section>
          <b-table
                  :data="data"
                  :total="total"
                  paginated
                  pagination-simple
                  backend-pagination
                  :per-page="perPage"
                  @page-change="onPageChange"
                  :loading="loading">
            <template slot-scope="props">
              <b-table-column>
                <figure class="image is-64x64">
                  <img :src="props.row.picture">
                </figure>
              </b-table-column>
              <b-table-column field="title" label="标题">
                <a @click="editModal(props.row)">
                  {{ props.row.title }}
                </a>
              </b-table-column>
              <b-table-column field="url" label="链接" width="100">
                <a :href="props.row.url" target="_blank">
                  <b-icon icon="external-link-square-alt"></b-icon>
                </a>
              </b-table-column>
              <b-table-column label="标签" width="100">
                <b-taglist>
                  <b-tag v-for="(tag, index) in props.row.tags" :key="index">{{tag}}</b-tag>
                </b-taglist>
              </b-table-column>
              <b-table-column field="category" label="原价/折扣" width="100">
                {{ props.row.msrp}} / {{ props.row.price}}
              </b-table-column>
              <b-table-column field="category" label="分类" width="100">
                {{ props.row.category}}
              </b-table-column>
              <b-table-column field="created_at" label="创建时间" width="160">
                {{ new Date(parseInt(props.row.created_at)) | moment('MM/DD/YYYY HH:mm')}}
              </b-table-column>
            </template>
          </b-table>
        </section>
      </div>
    </div>
  </div>
</template>

<script>
    import LeftBar from "./LeftBar"
    import EditModal from "./EditModal"

    export default {
        data() {
            return {
                data: [],
                total: 999,
                page: 1,
                perPage: 10,
                lastId: '',
                lastEpoch: '',
                loading: false,
                status: 'published',
                lastItem: null,
                forward: false
            }
        },
        components: {LeftBar},
        methods: {
            search() {
                const api = this.$store.state.api.url;
                this.loading = true;
                this.axios.get(api + '/deals?status=' + this.status + '&lastId=' + this.lastId + '&lastEpoch=' + this.lastEpoch + '&perPage=' + this.perPage + '&forward=' + this.forward, this.$data).then((response) => {
                    if (this.forward) {
                        this.data = response.data.items;
                        this.data.push(this.lastItem[0]);
                    } else {
                        this.data = response.data.items;
                    }
                    if (response.data.lastId) {
                        this.lastId = response.data.lastId.id;
                        this.lastEpoch = response.data.lastId.created_at;
                    }
                    this.loading = false;
                })
            },
            reset() {
                this.page = 1;
                this.lastId = '';
                this.lastEpoch = '';
                this.lastItem = null;
            },
            onPageChange(page) {
                if (page > this.page) {
                    if (!this.lastId) {
                        this.$toast.open({
                            duration: 1000,
                            message: '已到达最后一页',
                            type: 'is-success'
                        });

                        return false;
                    }
                    this.lastItem = this.data.splice(this.perPage - 1);
                    this.forward = false;
                } else {
                    if (!this.lastId) {
                        this.$toast.open({
                            duration: 1000,
                            message: '已到达第一页',
                            type: 'is-success'
                        });

                        return false;
                    }
                    this.forward = true;
                }
                this.search();
                this.page = page;
            },
            editModal(deal) {
                this.$modal.open({
                    parent: this,
                    component: EditModal,
                    hasModalCard: true,
                    props: {
                        deal: {
                            id: deal.id,
                            title: deal.title,
                            url: deal.url,
                            picture: deal.picture,
                            category: deal.category,
                            status: deal.status,
                            tags: deal.tags ? deal.tags : [],
                            msrp: deal.msrp,
                            price: deal.price,
                            coupon: deal.coupon,
                            body: deal.body,
                            created_at: deal.created_at
                        }
                    },
                    events: {
                        'deal-updated': self => {
                            self.close();
                            this.reset();
                            this.search(false);
                        }
                    }
                })
            }
        },
        watch: {
            status: function () {
                this.reset();
                this.search(false);
            }
        },
        mounted() {
            this.search();
        }
    }
</script>

<style>
</style>
