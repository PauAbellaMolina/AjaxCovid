<template>
    <div class="col-xl-3 col-md-6">
        <div class="card mb-3">
            <div class="row no-gutters h-100">
                <div class="col-md-4 bg-warning text-white text-center">
                    <div class="card-body">
                        <h1 class="card-text"><i class="fas fa-bug"></i></h1>
                    </div>
                </div>
                <div class="col-md-8">
                    <div class="card-body elem-mid">
                        <h2 class="card-text text-center" id="current_infected">{{ this.infectats }}</h2>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: {
      infectats: Number,
    }
  }
</script>