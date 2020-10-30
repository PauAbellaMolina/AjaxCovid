<template>
    <div class="col-xl-3 col-md-6">
        <div class="card mb-3">
            <div class="row no-gutters h-100">
                <div class="col-md-4 bg-success text-white text-center">
                    <div class="card-body">
                        <h1 class="card-text"><i class="fas fa-heartbeat"></i></h1>
                    </div>
                </div>
                <div class="col-md-8">
                    <div class="card-body elem-mid">
                        <h2 class="card-text text-center" id="current_recovered">{{ this.recuperats }}</h2> <!-- Mostrem la varible recuperats rebuda per props -->
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: {
      recuperats: Number, //Definim que rebrem per props una dada de tipus Number
    }
  }
</script>