<template>
  <div>
    <deal-editor :deal="deal" editor-title="Edit deal" editor-submit="Update" @deal-saved="update"></deal-editor>
    <b-loading :is-full-page="false" :active.sync="isLoading"></b-loading>
  </div>
</template>

<script>
    import DealEditor from "./DealEditor"

    export default {
        data() {
            return {
                isLoading: false
            }
        },
        props: ['deal'],
        components: {DealEditor},
        methods: {
            update(deal) {
                this.isLoading = true;
                const api = this.$store.state.api.url;
                const tmp = document.createElement('DIV');
                tmp.innerHTML = deal.body;
                deal.txt = tmp.textContent || tmp.innerText || '';
                this.axios.put(api + '/deals/' + deal.id, deal).then(() => {
                    this.isLoading = false;
                    this.$toast.open({
                        duration: 3000,
                        message: 'Deal已更新',
                        type: 'is-success'
                    });
                    this.$emit('deal-updated', this.$parent);
                })
            }
        }
    }
</script>
