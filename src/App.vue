<template>
    <div id="app" data-form-id="1">
        <!--<SimpleFormPage></SimpleFormPage>-->
        <DialogMain></DialogMain>
    </div>
</template>

<script>
    // import SimpleFormPage from './components/simple/SimpleFormPage.vue'
    import DialogMain from './components/dialog/DialogMain.vue'

    export default {
        name: 'App',
        components: {
            // SimpleFormPage,
            DialogMain,
        }
    }
</script>

<style src="@/assets/styles/app.css"></style>
