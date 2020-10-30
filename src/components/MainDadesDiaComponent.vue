<template>
    <div class="card mb-3">
        <div class="row no-gutters h-100">
            <div class="col-12">
                <div class="card-body">
                    <div><i class="fas fa-calendar-alt text-primary"></i> {{ this.dades.dateRep }}</div> <!-- Mostrem el camp data de les dades rebudes -->
                    <div><i class="fas fa-bug text-warning"></i> {{ this.dades.cases }}</div> <!-- Mostrem el camp casos de les dades rebudes -->
                    <div><i class="fas fa-skull-crossbones text-danger"></i> {{ this.dades.deaths }}</div> <!-- Mostrem el morts casos de les dades rebudes -->
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: {
      dades: Object, //Definim que rebrem per props una dada de tipus Object
    },
  }
</script>