<template>
    <div class="col my-3">
        <h4>Edit invoice</h4>
        <hr>
        <div v-if="errors" class="alert alert-danger alert-dismissible fade show" role="alert">
            {{ errors }}
        </div>
        <form @submit.prevent="updateInvoice">
            <div class="row">
                <div class="col">
                    <label for="number">Number</label>
                    <input type="text" class="form-control" v-model="number" id="number" placeholder="Number">
                </div>
                <div class="col">
                    <label for="supplied_at">Supply date</label>
                    <input type="date" class="form-control" id="supplied_at" v-model="supplied_at" placeholder="date">
                </div>
                <div class="col-12 mt-3">
                    <label for="comment">Comment</label>
                    <textarea class="form-control" id="comment" v-model="comment" placeholder="Your comment"></textarea>
                </div>
                <div class="col-12 my-3">
                    <button class="btn btn-primary float-right" type="submit">Edit</button>
                </div>
                <div class="clearfix"></div>
            </div>
        </form>
    </div>
</template>

<script>

export default {
    props: ['user', 'invoice'],
    data(){
        return {
            id: this.invoice.id,
            number: this.invoice.number,
            supplied_at: this.invoice.supplied_at,
            comment: this.invoice.comment,
            errors: null
        }
    },
    methods: {
        updateInvoice: async function() {
            const headers = {'Authorization' : 'Bearer '+ this.user.api_token};
            await axios.put('/api/invoices/'+this.id,
                { number: this.number, comment: this.comment, supplied_at: this.supplied_at },
                {headers})
                .then(res => {
                    this.invoice.number = res.data.invoice.number;
                    this.invoice.supplied_at = res.data.invoice.supplied_at;
                    this.invoice.comment = res.data.invoice.comment;
                    this.$emit('invoiceUpdated', { invoice: res.data.invoice });
                }).catch((e) => {
                    const status = e.response.status;
                    this.$notify({
                        group: 'invoiceAlert',
                        title: 'Error',
                        text: status === 422 ? Object.values(e.response.data.errors)[0][0] : e.response.data.message,
                        type: 'error'
                    });
                });
        }
    }
}
</script>
