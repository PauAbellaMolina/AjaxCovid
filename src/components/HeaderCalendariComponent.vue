<template>
    <div class="col-xl-3 col-md-6">
        <div class="card mb-3">
            <div class="row no-gutters h-100">
                <div class="col-md-4 bg-primary text-white text-center">
                    <div class="card-body">
                        <h1 class="card-text"><i class="far fa-calendar-alt"></i></h1>
                    </div>
                </div>
                <div class="col-md-8">
                    <div class="card-body elem-mid">
                        <h4 class="card-text text-center" id="current_date">{{this.data}}</h4> <!-- Mostrem la varible data rebuda per props -->
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: {
      data: String, //Definim que rebrem per props una dada de tipus String
    }
  }
</script>