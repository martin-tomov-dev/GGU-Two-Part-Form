<template>
    <div class="content-wrapper">
        <div class="content" style="width: 100%;">
            <div class="sqs-layout sqs-grid-12 columns-12" data-type="page-section"
                 id="page-section-6041b5cee5570c37ff55b18a">
                <div class="row sqs-row">
                    <div class="col sqs-col-12 span-12">
                        <div class="sqs-block code-block sqs-block-code">
                            <DialogFormIntro v-if="currentSection=='intro'" @showSection="showSection" title="My journey with Vue"></DialogFormIntro>
                            <DialogFormPage v-else @showSection="showSection" v-bind:motivation="motivation"></DialogFormPage>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    import DialogFormIntro from './DialogFormIntro.vue'
    import DialogFormPage from './DialogFormPage.vue'

    export default {
        name: "DialogMain",
        components: {
            DialogFormIntro,
            DialogFormPage
        },
        data() {
            return {
                currentSection : 'intro',
                motivation: ''
            }
        },
        methods:{
            showSection(data){
                this.currentSection = data.section;
                this.motivation = data.motivation;
            }
        }

    }
</script>

<style scoped>

</style>