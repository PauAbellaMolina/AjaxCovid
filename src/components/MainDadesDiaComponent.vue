<template>
    <div class="card mb-3">
        <div class="row no-gutters h-100">
            <div class="col-12">
                <div class="card-body">
                    <div><i class="fas fa-calendar-alt text-primary"></i> {{ this.dades.dateRep }}</div>
                    <div><i class="fas fa-bug text-warning"></i> {{ this.dades.cases }}</div>
                    <div><i class="fas fa-skull-crossbones text-danger"></i> {{ this.dades.deaths }}</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: {
      dades: Object,
    },
    beforeMount() {
        this.test();
    },
    methods: {
        test() {
            // console.log(this.dades)
        }
    }
  }
</script>