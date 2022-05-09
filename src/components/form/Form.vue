<template>
    <form @submit.prevent="handleSubmit">
        <div class="field-list">
            <div class="row sqs-row">
                <div class="col sqs-col-12 span-12">
                    <div class="form-item field firstname required">
                        <input class="field-element field-control" type="text"
                               placeholder="First name *"
                               v-model="form.firstname"/>
                    </div>
                    <div v-bind:class="(showError && !form.firstname)? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please enter your first name</span>
                    </div>
                    <div class="form-item field lastname">
                        <input class="field-element field-control" type="text"
                               placeholder="Last name *"
                               v-model="form.lastname"/>
                    </div>
                    <div v-bind:class="(showError && !form.lastname)? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please enter your last name</span>
                    </div>
                    <div class="form-item field select">
                        <!--//ggu_AreaofInterest-->
                        <select class="field-element" v-model="selectedAreaOfInterestValues">
                            <option disabled value="">
                                Select Field of Study*
                            </option>
                            <option v-for="(areaOfInterest,label) in areaOfInterestsMap"
                                    :key="label"
                                    :value="areaOfInterest">
                                {{label }}
                            </option>
                        </select>
                    </div>
                    <div v-bind:class="(showError && !selectedAreaOfInterestValues)? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please select a field of study</span>
                    </div>

                    <div class="form-item field select">
                        <select class="field-element" v-model="selectedAcademicValue"
                                v-bind:disabled="selectedAreaOfInterestValues==''">
                            <option disabled value="">Select Program of
                                Interest*
                            </option>
                            <optgroup label="Graduate"
                                      v-show="GRProgramOfInterests.length>0">
                                <option v-for="(academicvalue, index ) in GRProgramOfInterests"
                                        :key="academicvalue.academic_program + index"
                                        v-bind:value="academicvalue">
                                    {{ academicvalue.academic_program_label }}
                                </option>
                            </optgroup>
                            <optgroup label="Undergraduate"
                                      v-show="UGProgramOfInterests.length>0">
                                <option v-for="(academicvalue, index ) in UGProgramOfInterests"
                                        :key="academicvalue.academic_program + index"
                                        v-bind:value="academicvalue">
                                    {{ academicvalue.academic_program_label }}
                                </option>
                            </optgroup>

                        </select>
                    </div>
                    <div v-bind:class="(showError && !selectedAcademicValue)? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please select a program of interest</span>
                    </div>

                    <div class="form-item field select">
                        <select class="field-element" v-model="entryterm" v-bind:disabled="selectedAcademicValue==''">
                            <option disabled value="">Select Anticipated
                                Start Date*
                            </option>
                            <option v-for="(entryterm, index) in entryterms"
                                    v-bind:key="index"
                                    v-bind:value="index">{{
                                entryterm }}
                            </option>
                        </select>
                    </div>

                    <div v-bind:class="(showError && !entryterm)? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please select an anticipated date of start</span>
                    </div>

                    <div class="form-item field">
                        <input class="field-element field-control" type="text"
                               placeholder="Email Address *"
                               v-model="form.emailaddress1"/>
                    </div>
                    <div v-bind:class="(showError && !this.isValidEmail(form.emailaddress1))? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please enter a valid email address.</span>
                    </div>
                    <div class="form-item field">
                        <input class="field-element field-control" type="tel" autocomplete="tel"
                               placeholder="Phone Number *" @input="acceptMobileNumber"
                               v-model="form.mobile"/>
                    </div>
                    <div v-bind:class="(showError && !this.isValidPhoneNumber(form.mobile))? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please enter a valid mobile number.</span>
                    </div>
                    <div class="form-item field">
                        <input class="field-element field-control" type="text"
                               placeholder="Zip Code *"
                               v-model="form.ggu_gguzipcode"/>
                    </div>
                    <div v-bind:class="(showError && !this.isValidZipCode(form.ggu_gguzipcode))? 'lb-error' : 'lb-hidden'">
                        <span class="lb-error-msg">Please enter a valid zip code.</span>
                    </div>
                </div>
            </div>
        </div>

        <div data-animation-role="button"
             class="form-button-wrapper form-button-wrapper--align-center ">
            <input class="button submit-button" type="submit"
                   value="Request Information">
        </div>
    </form>
</template>

<script>
    function groupBy(array, keyColName, valueColName) {
        const result = {}
        array.forEach(item => {
            let itemKey = item[keyColName];
            let itemValue = item[valueColName];
            if (!result[itemKey]) {
                result[itemKey] = []
            }
            result[itemKey].push(itemValue)
        });
        return result
    }

    import axios from 'axios';
    import moment from 'moment';

    export default {
        name: 'Form',
        components: {},
        props: {
            msg: String
        },
        computed: {
            areaOfInterestsMap: function () {
                return groupBy(this.areaOfInterests, 'label', 'value');
            },

            GRProgramOfInterests: function () {
                return this.filterPOI("GR");
            },
            UGProgramOfInterests: function () {
                return this.filterPOI("UG");
            },

        },
        watch: {
            'selectedAreaOfInterestValues': function () { //newValue
                // console.log('selectedAreaOfInterestValues changed ' + newValue);
                this.selectedAcademicValue = "";
            },
            'selectedAcademicValue': function (newValue) {
                // console.log('selectedAcademicValue changed ', newValue);
                this.entryterm = "";
                this.form.academiclevel = newValue.academic_level;
                // this.form.ggu_AreaofInterest = newValue.area_of_interest;
                this.form.ggu_AreaofInterest = newValue.reduced_area_of_interest;
                this.form.ggu_programtype = newValue.program_type;
                this.form.programofinterest = newValue.academic_program;
                if (this.selectedAcademicValue.academic_level_label == 'Graduate') {
                    this.entryterms = {
                        '22/UA': 'Summer 2022 - Starts May 8th',
                        '22/FA': 'Fall 2022 - Starts September 4th',
                        '23/SA': 'Spring 2023 - Starts January 8th',
                    }
                }
                else {
                    this.entryterms = {
                        '22/UA/AB': 'Summer 2022 Term A/B - Starts May 8th',
                        '22/UA/C': 'Summer 2022 Term C - Starts June 29th',
                        '22/FA/AB': 'Fall 2022 Term A/B - Starts September 4th',
                        '22/FA/C': 'Fall 2022 Term C - Starts October 26th',
                        '23/SA/AB': 'Spring 2023 Term A/B - Starts January 8th',
                        '23/SA/C': 'Spring 2023 Term C - Starts March 1st',
                    }
                }
            },
        },
        mounted() {
            console.log('Mounted');
        },
        methods: {
            xwwwfurlenc(srcjson) {
                if (typeof srcjson !== "object")
                    if (typeof console !== "undefined") {
                        console.log("\"srcjson\" is not a JSON object");
                        return null;
                    }
                let u = encodeURIComponent;
                var urljson = "";
                var keys = Object.keys(srcjson);
                for (var i = 0; i < keys.length; i++) {
                    urljson += u(keys[i]) + "=" + u(srcjson[keys[i]]);
                    if (i < (keys.length - 1)) urljson += "&";
                }
                return urljson;
            },
            acceptMobileNumber() {
                let x = this.form.mobile.replace(/\D/g, '').match(/(\d{0,3})(\d{0,3})(\d{0,4})/);
                this.form.mobile = !x[2] ? x[1] : '(' + x[1] + ') ' + x[2] + (x[3] ? '-' + x[3] : '');
            },
            currentDate: function () {
                return moment().format('MM/DD/YYYY');
            },
            filterPOI: function (academic_level) {
                let selectedAreaOfInterestValues = this.selectedAreaOfInterestValues;
                if (!selectedAreaOfInterestValues) {
                    return [];
                }
                return this.academicvalues.filter(function (academicvalue) {
                    //academicvalue.area_of_interest may contain
                    // several interest codes/values separated by comma
                    let selected = false;
                    let reducedInterests = [];
                    let count = 0;
                    let area_of_interest_values = academicvalue.area_of_interest.split(',');

                    area_of_interest_values.forEach(function (areaOfInterest) {
                        areaOfInterest = areaOfInterest.trim();
                        if (academic_level == academicvalue.academic_level && selectedAreaOfInterestValues.includes(areaOfInterest)) {
                            selected = true;
                            count++;
                            reducedInterests.push(areaOfInterest);
                            academicvalue.reduced_area_of_interest = areaOfInterest;
                        }

                    });
                    if (selected) {
                        console.log(academicvalue.academic_program_label + " count: " + count, " - ", reducedInterests);
                        if (count > 1) {
                            console.error("-------------------------------2 counts-------------");
                        }
                    }

                    return selected; //academicvalue.area_of_interest.includes();
                });
            },

            isValidEmail(email) {
                const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
                return re.test(String(email).toLowerCase());
            },
            isValidZipCode(zipCode) {
                let isValidZip = /(^\d{5}$)|(^\d{5}-\d{4}$)/.test(zipCode);
                return isValidZip;
            },
            isValidPhoneNumber(phone) {
                return phone.length == 14;
            },

            isFormValid() {
                console.log(this.form.firstname
                    , this.form.lastname
                    , this.selectedAreaOfInterestValues
                    , this.selectedAcademicValue
                    , this.entryterm
                    , this.isValidEmail(this.form.emailaddress1)
                    , this.isValidZipCode(this.form.ggu_gguzipcode)
                    , this.isValidPhoneNumber(this.form.mobile));

                return this.form.firstname
                    && this.form.lastname
                    && this.selectedAreaOfInterestValues
                    && this.selectedAcademicValue
                    && this.entryterm
                    && this.isValidEmail(this.form.emailaddress1)
                    && this.isValidZipCode(this.form.ggu_gguzipcode)
                    && this.isValidPhoneNumber(this.form.mobile);
            },

            handleSubmit() {
                console.log('handleSubmit');

                if (this.isFormValid()) {
                    console.log("Form data is valid");
                    this.showError = false;
                    this.submitForm();
                } else {
                    this.showError = true;
                    console.log("Form data is invalid");
                }
            },

            submitForm() {
                console.log('submitForm');
                let url_string = window.location.href;
                let url = new URL(url_string);

                this.form.inquiry_date = moment().format('MM/DD/YYYY'); //"01/18/2021";
                this.form.mobile = this.form.mobile.replace(/[^0-9]/g, '');

                let channel = url.searchParams.get("channel");
                if (channel) {
                    this.form.ggu_channel = channel;
                }

                let source = url.searchParams.get("source");
                if (source) {
                    this.form.ggu_source = source;
                }

                let utm_source = url.searchParams.get("utm_source");
                if (utm_source) {
                    this.form.ggu_utm_source = utm_source;
                }

                let utm_medium = url.searchParams.get("utm_medium");
                if (utm_medium) {
                    this.form.ggu_utm_medium = utm_medium;
                }

                let utm_campaign = url.searchParams.get("utm_campaign");
                if (utm_campaign) {
                    this.form.ggu_utm_campaign = utm_campaign;
                }

                let utm_term = url.searchParams.get("utm_term");
                if (utm_term) {
                    this.form.ggu_utm_term = utm_term;
                }

                let utm_content = url.searchParams.get("utm_content");
                if (utm_content) {
                    this.form.ggu_utm_content = utm_content;
                }
                this.form.entryterm = this.entryterm.slice(0,5);
                const requestOne = axios.post(`https://www.ggu.edu/GGURFI`, this.form);

                //requestTwo
                axios.all([requestOne]).then(axios.spread((...responses) => {
                    const responseOne = responses[0]
                    // const responseTwo = responses[1]

                    // use/access the results
                    console.log(responseOne);
                    // console.log(responseTwo);

                    // let status = response.status;
                    //(status >= 200 && status < 300)
                    this.responseData = responseOne.data;
                    //
                    if (this.responseData.success == true) {
                        let redirect_url = encodeURI(this.responseData.url + '&utm_clientid=VirtualVC');
                        console.log(redirect_url);


                        fetch('https://script.google.com/macros/s/AKfycbxiog8u_QX962bnHWo7Yn48F68VCLOmVON68Hu0tvYpElj0O6E/exec', {
                            method: 'POST',
                            headers: {
                                'Content-Type': 'application/x-www-form-urlencoded',
                                // 'Access-Control-Allow-Origin': 'https://military.ggu.edu'
                            },
                            body: this.xwwwfurlenc(this.form)
                        }).then(response => {

                            console.log(response);
                            // window.location.href = "https://military.ggu.edu/thank-you";
                            window.location.href = redirect_url;

                        }).catch(errors => {
                            console.warn(errors);
                        });


                    }

                })).catch(errors => {
                    console.warn(errors);
                });
            },
        },

        data() {
            return {
                showError: false,
                payloadString: '',
                responseData: '',
                entryterm: '',
                selectedAreaOfInterestValues: '',
                selectedAcademicValue: '',
                testFormData: {
                    "firstname": "TestRezwanVuejs",
                    "lastname": "Archer",
                    "emailaddress1": "testjan26@eckmandesign.com",
                    "mobile": "6264224311",
                    "ggu_phone": "",
                    "preferredphone": "Mobile Phone",
                    "ggu_gguzipcode": "91711",
                    "veteranflag": "yes",
                    "entryterm": "21/UA",
                    "academiclevel": "GR",
                    "ggu_programtype": "DS-GRAD",
                    "ggu_AreaofInterest": "BA.GR",
                    "programofinterest": "MBA.BUSA",
                    "ggu_channel": "c-web",
                    "ggu_source": "facebook",
                    "ggu_utm_source": "facebook",
                    "ggu_utm_medium": "cpm",
                    "ggu_utm_clientId": "VirtualVC",
                    "ggu_utm_campaign": "mobilevetscalifornia",
                    "ggu_utm_term": "fbnf",
                    "ggu_utm_content": "general",
                    "inquiry_source": "ggurfi",
                    "inquiry_date": "01/26/2021"
                },
                form: {
                    firstname: '',
                    lastname: '',
                    emailaddress1: '',
                    mobile: '',
                    ggu_phone: '',
                    preferredphone: 'Mobile Phone',
                    ggu_gguzipcode: '',
                    veteranflag: 'yes',
                    entryterm: '',
                    academiclevel: '',
                    ggu_programtype: '',
                    ggu_AreaofInterest: '',
                    programofinterest: '',
                    ggu_channel: 'C-Web',
                    ggu_source: 'Web RFI',
                    ggu_utm_source: 'rfi',
                    ggu_utm_medium: 'organic',
                    ggu_utm_clientId: 'VirtualVC',
                    //
                    ggu_utm_campaign: '',
                    ggu_utm_term: '',
                    ggu_utm_content: '',
                    //
                    inquiry_source: 'ggurfi',
                    inquiry_date: '',
                },
                entryterms: {
                    '22/UA/AB': 'Summer 2022 Term A/B - Starts May 8th',
                    '22/UA/C': 'Summer 2022 Term C - Starts June 29th',
                    '22/FA/AB': 'Fall 2022 Term A/B - Starts September 4th',
                    '22/FA/C': 'Fall 2022 Term C - Starts October 26th',
                    '23/SA/AB': 'Spring 2023 Term A/B - Starts January 8th',
                    '23/SA/C': 'Spring 2023 Term C - Starts March 1st',
                },
                areaOfInterests: [
                    {
                        "label": "Business & Management",
                        "value": "MGMT.GR"
                    },
                    {
                        "label": "Business & Management",
                        "value": "MGMT.UC"
                    },
                    {
                        "label": "Business & Management",
                        "value": "MGMT.UD"
                    },
                    {
                        "label": "Accounting",
                        "value": "ACTG.GC"
                    },
                    {
                        "label": "Accounting",
                        "value": "ACTG.GR"
                    },
                    {
                        "label": "Accounting",
                        "value": "ACTG.UC"
                    },
                    {
                        "label": "Accounting",
                        "value": "ACTG.UD"
                    },
                    {
                        "label": "Business Analytics",
                        "value": "BA.GC"
                    },
                    {
                        "label": "Business Analytics",
                        "value": "BA.GR"
                    },
                    {
                        "label": "Business Analytics",
                        "value": "BA.PC"
                    },
                    {
                        "label": "Business Analytics",
                        "value": "BA.UC"
                    },
                    {
                        "label": "Business Analytics",
                        "value": "BA.UD"
                    },
                    {
                        "label": "Finance",
                        "value": "FIN.GC"
                    },
                    {
                        "label": "Finance",
                        "value": "FIN.GR"
                    },
                    {
                        "label": "Finance",
                        "value": "FIN.UC"
                    },
                    {
                        "label": "Finance",
                        "value": "FIN.UD"
                    },
                    {
                        "label": "Financial Planning",
                        "value": "FINP.GC"
                    },
                    {
                        "label": "Financial Planning",
                        "value": "FINP.GR"
                    },
                    {
                        "label": "Financial Planning",
                        "value": "FP.PC"
                    },
                    {
                        "label": "General Studies - Non Degree",
                        "value": "GS.GND"
                    },
                    {
                        "label": "General Studies - Degree Seeking",
                        "value": "GS.GR"
                    },
                    {
                        "label": "General Studies",
                        "value": "GS.UD"
                    },
                    {
                        "label": "General Studies",
                        "value": "GS.UND"
                    },
                    {
                        "label": "Human Resources",
                        "value": "HR.GC"
                    },
                    {
                        "label": "Human Resources",
                        "value": "HR.GR"
                    },
                    {
                        "label": "Human Resources",
                        "value": "HR.PC"
                    },
                    {
                        "label": "Human Resources",
                        "value": "HR.UC"
                    },
                    {
                        "label": "Human Resources",
                        "value": "HR.UD"
                    },
                    {
                        "label": "International Business",
                        "value": "IB.GR"
                    },
                    {
                        "label": "International Business",
                        "value": "IB.UC"
                    },
                    {
                        "label": "International Business",
                        "value": "IB.UD"
                    },
                    {
                        "label": "Information Technology Management",
                        "value": "ITM.GC"
                    },
                    {
                        "label": "Information Technology Management",
                        "value": "ITM.GR"
                    },
                    {
                        "label": "Information Technology Management",
                        "value": "ITM.UC"
                    },
                    {
                        "label": "Information Technology Management",
                        "value": "ITM.UD"
                    },
                    {
                        "label": "Law",
                        "value": "LAW.GR"
                    },
                    {
                        "label": "Leadership",
                        "value": "LDR.GC"
                    },
                    {
                        "label": "Leadership",
                        "value": "LDR.GR"
                    },
                    {
                        "label": "Leadership",
                        "value": "LDR.PC"
                    },
                    {
                        "label": "Leadership",
                        "value": "LDR.UC"
                    },
                    {
                        "label": "Leadership",
                        "value": "LDR.UD"
                    },
                    {
                        "label": "Master of Business Administration / Juris Doctor (Joint Degree)",
                        "value": "MBA.JD.GR"
                    },
                    {
                        "label": "Marketing",
                        "value": "MKT.GC"
                    },
                    {
                        "label": "Marketing",
                        "value": "MKT.GR"
                    },
                    {
                        "label": "Marketing",
                        "value": "MKT.UC"
                    },
                    {
                        "label": "Marketing",
                        "value": "MKT.UD"
                    },
                    {
                        "label": "Operations Management",
                        "value": "OP.GR"
                    },
                    {
                        "label": "Operations Management",
                        "value": "OP.UC"
                    },
                    {
                        "label": "Operations Management",
                        "value": "OP.UD"
                    },
                    {
                        "label": "Public Administration",
                        "value": "PA.GC"
                    },
                    {
                        "label": "Public Administration",
                        "value": "PA.GR"
                    },
                    {
                        "label": "Public Administration",
                        "value": "PA.UC"
                    },
                    {
                        "label": "Public Administration",
                        "value": "PA.UD"
                    },
                    {
                        "label": "Project Management",
                        "value": "PM.GC"
                    },
                    {
                        "label": "Project Management",
                        "value": "PM.GR"
                    },
                    {
                        "label": "Project Management",
                        "value": "PM.PC"
                    },
                    {
                        "label": "Project Management",
                        "value": "PM.UC"
                    },
                    {
                        "label": "Psychology",
                        "value": "PSY.GC"
                    },
                    {
                        "label": "Psychology",
                        "value": "PSY.GR"
                    },
                    {
                        "label": "Psychology",
                        "value": "PSY.UD"
                    },
                    {
                        "label": "Supply Chain Management",
                        "value": "SCM.GC"
                    },
                    {
                        "label": "Supply Chain Management",
                        "value": "SCM.GR"
                    },
                    {
                        "label": "Taxation",
                        "value": "TX.GC"
                    },
                    {
                        "label": "Taxation",
                        "value": "TX.GR"
                    }
                ],

                academicvalues: [
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.GR",
                        "academic_program_label": "Master of Arts in Counseling Psychology",
                        "academic_program": "MA.COUN.PSY"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.GR",
                        "academic_program_label": "Master of Arts in Industrial-Organizational Psychology",
                        "academic_program": "MA.IOPSY"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Business Administration (Adaptive Leadership Concentration)",
                        "academic_program": "MBA.ADL"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.GR",
                        "academic_program_label": "Master of Business Administration (Business Analytics Concentration)",
                        "academic_program": "MBA.BDA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ACTG.GR, MGMT.GR, BA.GR, FIN.GR, HR.GR, ITM.GR, IB.GR, LAW.GR, LDR.GR, MKT.GR, OP.GR, PM.GR, PA.GR, SCM.GR",
                        "academic_program_label": "Master of Business Administration",
                        "academic_program": "MBA.BUSA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.GR",
                        "academic_program_label": "Master of Business Administration (Entrepreneurship Concentration)",
                        "academic_program": "MBA.BUSA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.GR",
                        "academic_program_label": "Master of Business Administration (Gilead)",
                        "academic_program": "MBA.BUSA.GILD.13"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.GR",
                        "academic_program_label": "Executive Masters of Business Administration",
                        "academic_program": "MBA.EMBA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Finance ",
                        "area_of_interest": "FIN.GR",
                        "academic_program_label": "Master of Business Administration (Finance Concentration)",
                        "academic_program": "MBA.FIN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Human Resources",
                        "area_of_interest": "HR.GR",
                        "academic_program_label": "Master of Business Administration (Human Resource Management Concentration)",
                        "academic_program": "MBA.HUMR"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Information Technology Management",
                        "area_of_interest": "ITM.GR",
                        "academic_program_label": "Master of Business Administration (Information Technology Management Concentration)",
                        "academic_program": "MBA.IT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "LAW.GR, MBA.JD.GR",
                        "academic_program_label": "Master of Business Administration (Law - Juris Doctor(JD)/MBA)**",
                        "academic_program": "MBA.LAW"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Marketing",
                        "area_of_interest": "MKT.GR",
                        "academic_program_label": "Master of Business Administration (Marketing Concentration)",
                        "academic_program": "MBA.MKT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.GR, PA.GR",
                        "academic_program_label": "Master of Business Administration (Public Administration Concentration)",
                        "academic_program": "MBA.PAD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.GR, PM.GR",
                        "academic_program_label": "Master of Business Administration (Project Management Concentration)",
                        "academic_program": "MBA.PMGT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.GR",
                        "academic_program_label": "Executive Master of Public Administration",
                        "academic_program": "MPA.EMPA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.GR",
                        "academic_program_label": "Executive Masters of Public Administration   (Law Enforcement & Security Concentration)",
                        "academic_program": "MPA.EMPA.LES"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.GR",
                        "academic_program_label": "Executive Master of Public Administration (Urban Innovations Concentration)",
                        "academic_program": "MPA.EMPA.UI"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GR",
                        "academic_program_label": "Master of Science in Accounting Data & Analytics",
                        "academic_program": "MS.ADA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GR",
                        "academic_program_label": "Master of Science in Accounting Data & Analytics (Adv. Analytics for Accountants Concentration)",
                        "academic_program": "MS.ADA.ADVA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GR",
                        "academic_program_label": "Master of Science in Accounting Data & Analytics (CPA Applied Accounting Concentration)",
                        "academic_program": "MS.ADA.CPAA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GR",
                        "academic_program_label": "Master of Science in Accounting Data & Analytics (Essentials of Leadership Concentration)",
                        "academic_program": "MS.ADA.LEAD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ACTG.GR, TX.GR",
                        "academic_program_label": "Master of Science in Accounting Data & Analytics (Taxation Concentration)",
                        "academic_program": "MS.ADA.TAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FINP.GR",
                        "academic_program_label": "Master of Science in Advanced Financial Planning (Estate Planning Concentration)",
                        "academic_program": "MS.AFP.ESTATE"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FINP.GR",
                        "academic_program_label": "Master of Science in Advanced Financial Planning (Financial Life Planning Concentration)",
                        "academic_program": "MS.AFP.FLP"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FINP.GR",
                        "academic_program_label": "Master of Science in Advanced Financial Planning (Taxation Concentration)",
                        "academic_program": "MS.AFP.TAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.GR",
                        "academic_program_label": "Master of Science in Business Analytics",
                        "academic_program": "MS.BA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.GR",
                        "academic_program_label": "Master of Science in Business Analytics (Management Concentration)",
                        "academic_program": "MS.BA.AM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.GR",
                        "academic_program_label": "Master of Science in Business Analytics (Marketing Concentration)",
                        "academic_program": "MS.BA.MKT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Finance ",
                        "area_of_interest": "FIN.GR",
                        "academic_program_label": "Master of Science in Finance",
                        "academic_program": "MS.FIN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Finance ",
                        "area_of_interest": "FIN.GR",
                        "academic_program_label": "Master of Science in Financial Analytics",
                        "academic_program": "MS.FINAN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FINP.GR",
                        "academic_program_label": "Master of Science in Financial Planning",
                        "academic_program": "MS.FIPL"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Human Resources",
                        "area_of_interest": "HR.GR",
                        "academic_program_label": "Master of Science in Human Resources Management",
                        "academic_program": "MS.HRM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Information Technology Management",
                        "area_of_interest": "ITM.GR",
                        "academic_program_label": "Master of Science in Information Technology Management",
                        "academic_program": "MS.ITM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ITM.GR, PM.GR",
                        "academic_program_label": "Master of Science in Information Technology (Project Management Concentration)",
                        "academic_program": "MS.ITM.PM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership",
                        "academic_program": "MS.LEAD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "BA.GR, LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Business Analytics Concentration)",
                        "academic_program": "MS.LEAD.BA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Finance Concentration)",
                        "academic_program": "MS.LEAD.FIN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Human Resource Management Concentration)",
                        "academic_program": "MS.LEAD.HRM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Influence and Conflict Mgmt Concenbration)",
                        "academic_program": "MS.LEAD.ICMG"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Information Technology Mgmt Concentration)",
                        "academic_program": "MS.LEAD.ITM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Marketing Concentration)",
                        "academic_program": "MS.LEAD.MKT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GR",
                        "academic_program_label": "Master of Science in Leadership (Org. Behavior and Development Concentration)",
                        "academic_program": "MS.LEAD.ORBD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "LDR.GR, PM.GR",
                        "academic_program_label": "Master of Science in Leadership (Project Management Concentration)",
                        "academic_program": "MS.LEAD.PM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Project Management",
                        "area_of_interest": "PM.GR",
                        "academic_program_label": "Master of Science in Project Management",
                        "academic_program": "MS.PM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GR",
                        "academic_program_label": "Master of Science in Taxation",
                        "academic_program": "MS.TAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GR",
                        "academic_program_label": "Master of Science in Taxation (Full-time Day Program)",
                        "academic_program": "MS.TXD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Open Enrollment",
                        "program_type": "OE-GRAD",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ACTG.GR, BA.GR, FIN.GR, FINP.GR, GS.GR, HR.GR, IB.GR, ITM.GR, LAW,GR, LDR.GR, MGMT.GR, MKT.GR, OP.GR, PA.GR, PM.GR, PSY.GR, SCM.GR, TX.GR",
                        "academic_program_label": "Graduate Degree Seeking Open Enrollment",
                        "academic_program": "OPEN.DGR"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Open Enrollment",
                        "program_type": "OE-GRAD",
                        "area_of_interest_label": "General Studies",
                        "area_of_interest": "GS.GND",
                        "academic_program_label": "Graduate Non-Degree Seeking Open Enrollment",
                        "academic_program": "OPEN.GR"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.PC",
                        "academic_program_label": "Graduate Credential in Organizational Leadership",
                        "academic_program": "GRCRD.LEAD"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GC",
                        "academic_program_label": "Accounting Foundation",
                        "academic_program": "CERTG.ACCTFND"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GC",
                        "academic_program_label": "Accounting & Data Analytics",
                        "academic_program": "CERTG.ADAN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GC",
                        "academic_program_label": "Advanced Studies in Taxation",
                        "academic_program": "CERTG.ADTAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GC",
                        "academic_program_label": "Advanced Analytics for Accountants",
                        "academic_program": "CERTG.ADVA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.GC",
                        "academic_program_label": "Conflict Resolution",
                        "academic_program": "CERTG.CNRES"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.GC",
                        "academic_program_label": "Counseling Skills",
                        "academic_program": "CERTG.COUN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.GC",
                        "academic_program_label": "CPA Applied Accounting",
                        "academic_program": "CERTG.CPAA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GC",
                        "academic_program_label": "Estate Planning",
                        "academic_program": "CERTG.ESTAT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FP.PC",
                        "academic_program_label": "Graduate Certificate in Financial Life Planning",
                        "academic_program": "CERTG.FLP"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GC",
                        "academic_program_label": "International Taxation",
                        "academic_program": "CERTG.INTAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.GC",
                        "academic_program_label": "Industrial-Organizational Psychology",
                        "academic_program": "CERTG.IOPSY"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Information Technology Management",
                        "area_of_interest": "ITM.GC",
                        "academic_program_label": "Information Technology",
                        "academic_program": "CERTG.IT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.GC",
                        "academic_program_label": "Public Administration Leadership",
                        "academic_program": "CERTG.PADL"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GC",
                        "academic_program_label": "State and Local Tax",
                        "academic_program": "CERTG.SALT"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Taxation",
                        "area_of_interest": "TX.GC",
                        "academic_program_label": "Taxation",
                        "academic_program": "CERTG.TAX"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Graduate Certificate",
                        "program_type": "C-GRAD",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.GC",
                        "academic_program_label": "Urban Innovations Graduate Certificate",
                        "academic_program": "CERTG.URBAN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.PC",
                        "academic_program_label": "Graduate Certificate in Analytics for Competitive Advantage",
                        "academic_program": "CERTG.ACA"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.PC",
                        "academic_program_label": "Graduate Certificate in Adaptive Leadership",
                        "academic_program": "CERTG.ADL"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Financial Planning",
                        "area_of_interest": "FP.PC",
                        "academic_program_label": "Financial Planning",
                        "academic_program": "CERTG.FINPL"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Human Resources",
                        "area_of_interest": "HR.PC",
                        "academic_program_label": "Human Resources Management",
                        "academic_program": "CERTG.HRM"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.GC",
                        "academic_program_label": "Essentials of Leadership",
                        "academic_program": "CERTG.LEAD.ESSEN"
                    },
                    {
                        "academic_level_label": "Graduate",
                        "academic_level": "GR ",
                        "program_type_label": "Professional Certificate",
                        "program_type": "GR-PC",
                        "area_of_interest_label": "Project Management",
                        "area_of_interest": "PM.PC",
                        "academic_program_label": "Project Management Practices (12 Units)",
                        "academic_program": "CERTG.PMGT12"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "General Studies",
                        "area_of_interest": "GS.UD",
                        "academic_program_label": "Associate of Arts in General Studies",
                        "academic_program": "AA.GENL"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, PSY.UD, PA.UD",
                        "academic_program_label": "Bachelor of Arts in Management",
                        "academic_program": "BA.MGMT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.UD",
                        "academic_program_label": "Bachelor of Arts in Management (Human Resource Management Concentration)",
                        "academic_program": "BA.MGMT.HUMR"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, ITM.UD",
                        "academic_program_label": "Bachelor of Arts in Management (Information Technology Concentration)",
                        "academic_program": "BA.MGMT.IT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, MKT.UD, PSY.UD",
                        "academic_program_label": "Bachelor of Arts in Management (Marketing Concentration)",
                        "academic_program": "BA.MGMT.MKT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, PA.UD",
                        "academic_program_label": "Bachelor of Arts in Management (Public Administration Concentration)",
                        "academic_program": "BA.MGMT.PAD"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, PSY.UD",
                        "academic_program_label": "Bachelor of Arts in Management (Psychology Concentration)",
                        "academic_program": "BA.MGMT.PSY"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, LDR.UD",
                        "academic_program_label": "Bachelor of Arts in Organizational Leadership and Human Skills Development",
                        "academic_program": "BA.OLHS"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "Psychology",
                        "area_of_interest": "PSY.UD",
                        "academic_program_label": "Bachelor of Arts  in Psychology",
                        "academic_program": "BA.PSYCH"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.UD",
                        "academic_program_label": "Bachelor of Science in Accounting",
                        "academic_program": "BS.ACCTG"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.UD",
                        "academic_program_label": "Bachelor of Science in Business (Accounting Concentration)",
                        "academic_program": "BS.BUSN.ACCT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, BA.UD",
                        "academic_program_label": "Bachelor of Science in Business (Data Analytics Conc)",
                        "academic_program": "BS.BUSN.DA"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, FIN.UD",
                        "academic_program_label": "Bachelor of Science in Business (Finance Concentration)",
                        "academic_program": "BS.BUSN.FIN"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ACTG.UD, MGMT.UD, BA.UD, FIN.UD, HR.UD, ITM.UD, IB.UD, MKT.UD, OP.UD, PSY.UD, PA.UD",
                        "academic_program_label": "Bachelor of Science in Business",
                        "academic_program": "BS.BUSN.GEN"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, HR.UD",
                        "academic_program_label": "Bachelor of Science in Business (Human Resources Management Concentration)",
                        "academic_program": "BS.BUSN.HUMR"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, IB.UD",
                        "academic_program_label": "Bachelor of Science in Business (International Business Concentration)",
                        "academic_program": "BS.BUSN.INTB"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, ITM.UD",
                        "academic_program_label": "Bachelor of Science in Business (Information Technology Concentration)",
                        "academic_program": "BS.BUSN.IT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, MKT.UD",
                        "academic_program_label": "Bachelor of Science in Business (Marketing Concentration)",
                        "academic_program": "BS.BUSN.MKT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, OP.UD",
                        "academic_program_label": "Bachelor of Science in Business (Operations/Supply Chain Management Conc)",
                        "academic_program": "BS.BUSN.OPSC"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, PA.UD",
                        "academic_program_label": "Bachelor of Science in Business (Public Administration Concentration)",
                        "academic_program": "BS.BUSN.PAD"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "MGMT.UD, PSY.UG",
                        "academic_program_label": "Bachelor of Science in Business (Psychology Concentration)",
                        "academic_program": "BS.BUSN.PSY"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Degree Seeking",
                        "program_type": "DS-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UD",
                        "academic_program_label": "Bachelor of Science in Data Analytics",
                        "academic_program": "BS.DA"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "NA",
                        "program_type": "NA",
                        "area_of_interest_label": "List areas of interest in next column as options ",
                        "area_of_interest": "ACTG.UD, BA.UD, FIN.UD, GS.UD, HR.UD, IB.UD, ITM.UD, LDR.UD, MGMT.UD, MKT.UD, OP.UD, PA.UD, PSY.UD",
                        "academic_program_label": "Undecided",
                        "academic_program": "BA.UD"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Open Enrollment",
                        "program_type": "OE-UG",
                        "area_of_interest_label": "General Studies",
                        "area_of_interest": "GS.UD",
                        "academic_program_label": "Undergraduate Degree Seeking Open Enrollment",
                        "academic_program": "OPEN.DUG"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Open Enrollment",
                        "program_type": "OE-UG",
                        "area_of_interest_label": "General Studies",
                        "area_of_interest": "GS.UND",
                        "academic_program_label": "Undergraduate Non-Degree Seeking Open Enrollment",
                        "academic_program": "OPEN.UG"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Accounting",
                        "area_of_interest": "ACTG.UC",
                        "academic_program_label": "Accounting (15 units)",
                        "academic_program": "CERTU.ACCT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.UC",
                        "academic_program_label": "Building & Sustaining Relationships for Organizational Success",
                        "academic_program": "CERTU.BSRE"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Programming with HQL",
                        "academic_program": "CERTU.DAHQL"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Programming with Python",
                        "academic_program": "CERTU.DAPYT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Programming with R",
                        "academic_program": "CERTU.DAR"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Programming with SAS",
                        "academic_program": "CERTU.DASAS"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Programming with SQL",
                        "academic_program": "CERTU.DASQL"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business Analytics",
                        "area_of_interest": "BA.UC",
                        "academic_program_label": "Data Analytics: Basic Proficiency in Data Visualization with Tablleau",
                        "academic_program": "CERTU.DAVT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Marketing",
                        "area_of_interest": "MKT.UC",
                        "academic_program_label": "Digital Marketing ",
                        "academic_program": "CERTU.DGMKT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Finance ",
                        "area_of_interest": "FIN.UC",
                        "academic_program_label": "Finance (UG)",
                        "academic_program": "CERTU.FIN"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Human Resources",
                        "area_of_interest": "HR.UC",
                        "academic_program_label": "Human Resource Management (UG)",
                        "academic_program": "CERTU.HRMGT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "International Business",
                        "area_of_interest": "IB.UC",
                        "academic_program_label": "International Business",
                        "academic_program": "CERTU.INTBS"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Information Technology Management",
                        "area_of_interest": "ITM.UC",
                        "academic_program_label": "Information Technology (UG)",
                        "academic_program": "CERTU.IT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Leadership",
                        "area_of_interest": "LDR.UC",
                        "academic_program_label": "Leading & Managing Through Change and Disruption",
                        "academic_program": "CERTU.LMCD"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.UC",
                        "academic_program_label": "Management",
                        "academic_program": "CERTU.MGT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Marketing",
                        "area_of_interest": "MKT.UC",
                        "academic_program_label": "Marketing (UG)",
                        "academic_program": "CERTU.MKTG"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Operations Management",
                        "area_of_interest": "OP.UC",
                        "academic_program_label": "Operations and Supply Chain Management",
                        "academic_program": "CERTU.OPMG"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Public Administration",
                        "area_of_interest": "PA.UC",
                        "academic_program_label": "Public Administration Leadership (UG)",
                        "academic_program": "CERTU.PADL"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Project Management",
                        "area_of_interest": "PM.UC",
                        "academic_program_label": "Project Management (9 units)",
                        "academic_program": "CERTU.PMGT"
                    },
                    {
                        "academic_level_label": "Undergraduate",
                        "academic_level": "UG",
                        "program_type_label": "Undergraduate Certificate",
                        "program_type": "C-UG",
                        "area_of_interest_label": "Business and Management",
                        "area_of_interest": "MGMT.UC",
                        "academic_program_label": "Salesforce for Business",
                        "academic_program": "CERTU.SFBS"
                    }
                ],
            }
        },
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped src="@/assets/styles/form.css"></style>