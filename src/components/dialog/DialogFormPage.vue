<template>
    <div>
        <div class="row sqs-row">
            <div class="col sqs-col-6 span-6">
                <div class="close">
                    <div>
                        <svg class="lb-hidden-small" width="7" height="12" fill="none"
                             xmlns="http://www.w3.org/2000/svg" focusable="false">
                            <path opacity=".75" fill-rule="evenodd" clip-rule="evenodd" d="M2.112 6l4.632-4.537a.845.845 0 000-1.212.888.888 0 00-1.238 0L.256 5.394a.845.845 0 000
                            1.212l5.25 5.143a.888.888 0 001.238 0 .845.845 0 000-1.212L2.112 6z" fill="#fff">
                            </path>
                        </svg>
                        <svg class="lb-hidden-big" width="7" height="12" fill="none" xmlns="http://www.w3.org/2000/svg"
                             focusable="false">
                            <path opacity=".75" fill-rule="evenodd" clip-rule="evenodd" d="M2.112 6l4.632-4.537a.845.845 0 000-1.212.888.888 0 00-1.238 0L.256 5.394a.845.845 0 000
                            1.212l5.25 5.143a.888.888 0 001.238 0 .845.845 0 000-1.212L2.112 6z" fill="#000">
                            </path>
                        </svg>
                        <span v-on:click="showIntro()" class="lb-previous-btn">Previous</span>
                    </div>

                </div>
            </div>
        </div>

        <div class="row sqs-row">
            <div class="col sqs-col-12 span-12 lb-center">
                <h5 class="lb-header-text">Want to {{ motivation }}?</h5>
            </div>
        </div>
        <div class="row sqs-row lb-main-container lb-layout">
            <div class="col sqs-col-6 span-6 lb-image-bg">
                <div class="sqs-block-content">
                    <h5 class="lb-header-text lb-hidden-small">Why study with GGU?</h5>
                    <div class="row sqs-row lb-hidden-small">
                        <div class="col sqs-col-2 span-2">
                            <div>
                                <img class="lb-testimonial-img" alt=""
                                     src="https://images.squarespace-cdn.com/content/v1/5fd2ad2e03498d08cdc522c3/b013261c-c42f-457d-8e4b-3f32eb258cea/ph.jpg">
                            </div>
                        </div>
                        <div class="col sqs-col-10 span-10">
                            <div class="sqs-block-content">
                                <p class="lb-testimonial-name">Zoilo Diaz-Jerez - Air force</p>
                                <p class="lb-testimonial-text">
                                    &quot;
                                    They accepted most of my transfer credits and offered
                                    hybrid/online courses. It really sets the school apart fro
                                    m similar programs...I'm able to stay home and spend the time
                                    that I would otherwise be commuting caring for my family
                                    instead. It&apos;s amazing. Connecting with fellow Veterans,
                                    regardless of branch of service,
                                    and supporting one another&#8212;just like we have each other's
                                    backs on the field.
                                    &quot;
                                </p>

                            </div>
                        </div>
                    </div>
                    <div class="lb-bah">
                        <div class="lb-dark-bg">
                            <img alt="" class="lb-hidden-small"
                                 src="https://images.squarespace-cdn.com/content/v1/5fd2ad2e03498d08cdc522c3/e6e9b6dc-4d4c-432b-9f0f-88ea59aed71e/what-about-bah.png">
                            <img alt="" src="https://images.squarespace-cdn.com/content/v1/5fd2ad2e03498d08cdc522c3/e6e9b6dc-4d4c-432b-9f0f-88ea59aed71e/what-about-bah.png"
                                 class="lb-hidden-big">
                        </div>

                        <div class="sqs-block-content lb-bah-heading">
                            <h4>
                                What about
                            </h4>
                            <h2>
                                BAH?
                            </h2>
                            <h5>
                                If you plan to use your Post-9/11 GI Bill, you may be eligible for a
                                housing allowance equal to the BAH r
                                ate for an E-5 with dependents in San Francisco, California,
                                $4,971/month.
                            </h5>

                        </div>
                    </div>
                </div>
            </div>
            <div class="col sqs-col-6 span-6">
                <div class="sqs-block-content lb-form-container">
                    <div class="form-wrapper">
                        <h4 class="lb-capitalize">{{ motivation }} with GGU!</h4>
                        <p>
                            Complete this simple form and request program information directly from
                            Golden Gate University
                        </p>

                        <div class="form-inner-wrapper lb-simple-form-inner-wrapper">
                            <Form msg="Simple Form"/>
                        </div>
                    </div>
                    <div class="lb-footer">Privacy guaranteed, we do not share your information</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Form from '../form/Form.vue'

    export default {
        name: "DialogFormPage",
        props: ['motivation'],
        components: {
            Form
        },
        methods: {
            showIntro() {
                this.$emit("showSection", {'section': 'intro'})
            }
        }
    }
</script>

<style scoped src="@/assets/styles/dialog-form-page.css"></style>