<template>
    <div class="row sqs-row">
        <div class="col sqs-col-12 span-12">
            <div class="sqs-block html-block sqs-block-html" data-block-type="2"
                 id="block-2002d29731787e9ee26c">
                <div class="sqs-block-content">
                    <h3 style="text-align:center;white-space:pre-wrap;">
                        <!--Ready for the next phase of your career? Reach out to us today.-->
                    </h3>
                </div>
            </div>

            <div class="row sqs-row ">
                <div class="col sqs-col-1 span-1">
                    <div class="sqs-block spacer-block sqs-block-spacer sized vsize-1"
                         data-block-type="21">
                        <div class="sqs-block-content">&nbsp;</div>
                    </div>
                </div>
                <div class="col sqs-col-6 span-6">
                    <div class="sqs-block-content lb-padding-lg-3">
                        <h2 class="lb-margin-sm-5px lb-title lb-text-white">
                            Learn more about GGU's programs for military veterans and their families.
                        </h2>
                        <h5 class="lb-text-white lb-subtitle-small">
                            Make the most of your Post-9/11 G.I. Bill&#xAE;&#xFE0F; benefits.
                        </h5>
                    </div>
                </div>
                <div class="col sqs-col-4 span-4">

                    <div class="form-wrapper lb-simple-form-wrapper">
                        <p class="lb-form-description">
                            Complete this simple form and request program information directly from
                            Golden Gate University
                        </p>
                        <div class="form-inner-wrapper lb-simple-form-inner-wrapper">
                            <Form msg="Simple Form"/>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Form from '../form/Form.vue'

    export default {
        name: "SimpleFormPage",
        components: {
            Form
        }
    }
</script>

<style scoped src="@/assets/styles/simple-form-page.css"></style>