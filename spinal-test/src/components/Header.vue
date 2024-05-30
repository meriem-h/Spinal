<template>
     <div>
          <figure class="conteneur">
               <img src="https://www.spinalcom.com/wp-content/themes/mkd-spinalcom/img/spinalcom_logo.svg"
                    alt="spinalcomLogo" class="logo">
               <!-- <figcaption>Légende de l'image</figcaption> -->
          </figure>
     </div>
</template>
 

<script>
export default {
     name: 'HeaderComponent',
}
</script>

<style>
.conteneur {
     background-color: #2E3244;
     margin: 0;
     padding: 1em;
     border-bottom-right-radius: 1rem;
     border-bottom-left-radius: 1rem;
}

.logo {
     height: 3rem;
}
</style>
   