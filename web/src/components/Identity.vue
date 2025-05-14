<script setup>
import { ref, computed } from "vue";
import { Form, Field, ErrorMessage } from "vee-validate";
import * as yup from "yup";
import moment from "moment";

const onSubmit = (values) => {
    fetch("http://esx_identity/register", {
        method: "POST",
        body: JSON.stringify({
            firstname: values.firstname,
            lastname: values.lastname,
            dateofbirth: moment(values.dob).format("DD/MM/YYYY"),
            sex: values.gender,
            height: values.height,
        }),
    });
};

const schema = yup.object({
    firstname: yup.string().required("Firstname is required").min(3, "Firstname must be at least 3 characters"),
    lastname: yup.string().required("Lastname is required").min(3, "Lastname must be at least 3 characters"),
    dob: yup.date().required("Date of Birth is required").min(new Date("1900-01-01"), "Date is too early").max(moment().subtract(1, "years").toDate(), "You need to be atleast 1 year old"),
    gender: yup.string().required("Gender is required"),
    height: yup.number().required("Height is required").min(120, "Minimum height is 120cm").max(220, "Maximum height is 220cm").typeError("Amount must be a number"),
});

const firstname = ref("");
const lastname = ref("");
const dob = ref("");
const height = ref(175);
const gender = ref("m");

const formattedDob = computed(() => {
    if (!dob.value) return "";
    return moment(dob.value).format("YYYY-MM-DD");
});

const age = computed(() => {
    if (!dob.value) return "";
    return moment().diff(moment(dob.value), 'years');
});

const getAvatar = () => {
    if (gender.value === "m") {
        return "https://fivem.lorisdev.fr/images/esx_identity/Jason_Duval.webp";
    } else {
        return "https://fivem.lorisdev.fr/images/esx_identity/Lucia_Caminos.webp";
    }
};

const getGenderLabel = (gender) => {
    if (gender === "m") {
        return "Homme";
    } else {
        return "Femme";
    }
};

const calculateAge = (dob) => {
    if (!dob) return "";
    return moment().diff(moment(dob), 'years');
};

const formatTextLong = (text) => {
    return text.length > 9 ? text.substring(0, 9) + "..." : text;
};
</script>

<template>
    <!-- <div class="dialog">
        <div class="dialog__header">
            <h1>CHARACTER <span>IDENTITY</span></h1>
        </div>
        <div class="dialog__body">
            <p class="dialog__body-hint">Start by creating your identity</p>
            <Form class="dialog__body-form" id="register" action="#" novalidate @submit="onSubmit" :validation-schema="schema">
                <div class="dialog__form-group">
                    <label for="firstname">Firstname</label>
                    <div class="dialog__form-validation">
                        <Field id="firstname" type="text" name="firstname" placeholder="Firstname" validateOnInput />
                    </div>
                    <ErrorMessage name="firstname" class="dialog__form-message dialog__form-message--error" />
                </div>
                <div class="dialog__form-group">
                    <label for="lastname">Lastname</label>
                    <div class="dialog__form-validation">
                        <Field id="lastname" type="text" name="lastname" placeholder="Lastname" validateOnInput />
                    </div>
                    <ErrorMessage name="lastname" class="dialog__form-message dialog__form-message--error" />
                </div>
                <div class="dialog__form-group">
                    <label for="dob">Date of birth</label>
                    <Field id="dob" type="date" name="dob" placeholder="dd/mm/yyyy" validateOnInput />
                    <ErrorMessage name="dob" class="dialog__form-message dialog__form-message--error" />
                </div>
                <div class="dialog__form-group">
                    <label for="gender">Gender</label>
                    <div class="dialog__form-group dialog__form-group--radio">
                        <div class="dialog__form-radio">
                            <Field type="radio" id="male" value="m" name="gender" validateOnInput />
                            <label for="male">
                                <i class="fas fa-mars"></i>Male
                            </label>
                        </div>
                        <div class="dialog__form-radio">
                            <Field type="radio" id="female" value="f" name="gender" validateOnInput />
                            <label for="female">
                                <i class="fas fa-venus"></i>Female
                            </label>
                        </div>
                    </div>
                    <ErrorMessage name="gender" class="dialog__form-message dialog__form-message--error" />
                </div>
                <div class="dialog__form-group">
                    <label for="height">Height</label>
                    <Field id="height" type="text" name="height" placeholder="175" validateOnInput/>
                    <ErrorMessage name="height" class="dialog__form-message dialog__form-message--error" />
                </div>
                <button class="dialog__form-submit" id="submit" type="submit">
                    <i class="fas fa-user-plus"></i>CREATE
                </button>
            </Form>
        </div>
    </div> -->
    <div class="identity" style="--color: #FCAF4F;">
        <div class="identity__header">
            <h1>CHARACTER <span>IDENTITY</span></h1>
            <p>Lorem Ipsum is simply dummy text of the printing and typesetting industry. typesetting industry. typesetting industry.typesetting industry.</p>
        </div>
        <div class="identity__body">
            <div class="identity__body-left">
                <div class="identity__body-left-police">
                    <div class="identity__body-left-police-image" style="background-image: url('https://s3-attachments.int-cdn.lcpdfrusercontent.com/monthly_2021_06/logo.png.8ce6adb12888e69b312a2aa00a8a7a04.png')"></div>
                    <h1>Police Department</h1>
                    <p>Join the force and help keep the city safe</p>
                </div>

                <div class="identity__body-left-card">
                    <div class="identity__body-left-card-photo" :style="{ backgroundImage: `url(${getAvatar()})` }"></div>
                    <div class="identity__body-left-card-info">
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">PRÉNOM</span>
                                <span class="identity__body-left-card-info-value">{{ formatTextLong(firstname) || "---" }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">NOM</span>
                                <span class="identity__body-left-card-info-value">{{ formatTextLong(lastname) || "---" }}</span>
                            </div>
                        </div>
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">DATE DE NAISSANCE</span>
                                <span class="identity__body-left-card-info-value">{{ formattedDob || "---" }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">SEXE</span>
                                <span class="identity__body-left-card-info-value">{{ getGenderLabel(gender) }}</span>
                            </div>
                        </div>
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">ÂGE</span>
                                <span class="identity__body-left-card-info-value">{{ age || "---" }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">TAILLE</span>
                                <span class="identity__body-left-card-info-value">{{ height }}cm</span>
                            </div>
                        </div>
                    </div>
                    <div class="identity__body-left-card-seal" style="background-image: url('https://fivem.lorisdev.fr/images/esx_identity/policeseal.jpg')">
                        <div class="identity__body-left-card-seal-image"></div>
                    </div>
                </div>
            </div>

            <div class="identity__body-center">
                <Form @submit="onSubmit" :validation-schema="schema" id="register" action="#" novalidate class="identity__body-center-form">
                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="firstName">PRÉNOM</label>
                                <Field id="firstname" type="text" name="firstname" placeholder="DIKKAT! Lütfen özel karakterler kullanmayıniz." validateOnInput v-model="firstname" />
                            </div>
                            <i class="fa-duotone fa-solid fa-address-card"></i>
                        </div>
                        <div class="identity__body-center-form-group-error">
                            <div class="identity__body-center-form-group-error-icon">
                                <i class="fa-solid fa-xmark"></i>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>Error</h1>
                                <ErrorMessage name="firstname" class="error-message" />
                            </div>
                        </div>
                    </div>

                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="lastName">NOM</label>
                                <Field id="lastname" type="text" name="lastname" placeholder="DIKKAT! Lütfen özel karakterler kullanmayıniz." validateOnInput v-model="lastname" />
                            </div>
                            <i class="fa-duotone fa-solid fa-address-card"></i>
                        </div>
                        <div class="identity__body-center-form-group-error">
                            <div class="identity__body-center-form-group-error-icon">
                                <i class="fa-solid fa-xmark"></i>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>Error</h1>
                                <ErrorMessage name="lastname" class="error-message" />
                            </div>
                        </div>
                    </div>

                    <!-- <div class="form-group">
                        <label for="birthdate">DATE DE NAISSANCE</label>
                        <Field id="dob" type="date" name="dob" placeholder="JJ/MM/AA" validateOnInput v-model="dob" />
                        <ErrorMessage name="dob" class="error-message" />
                    </div>

                    <div class="form-group">
                        <label for="height">TAILLE</label>
                        <div class="slider-container">
                            <input type="range" min="140" max="200" name="height" v-model="height" validateOnInput />
                            <span class="slider-value">{{ height }}</span>
                        </div>
                    </div>

                    <div class="form-group">
                        <label>SEXE</label>
                        <div class="gender-buttons">
                            <Field type="radio" id="male" value="m" name="gender" validateOnInput v-model="gender" />
                            <label for="male">HOMME</label>
                            <Field type="radio" id="female" value="f" name="gender" validateOnInput v-model="gender" />
                            <label for="female">FEMME</label>
                        </div>
                        <ErrorMessage name="gender" class="error-message" />
                    </div> -->

                    <button type="submit" class="submit-button">CRÉER PERSONNAGE</button>
                </Form>
            </div>

            <img src="https://fivem.lorisdev.fr/images/esx_identity/Raul_Bautista.webp" alt="Raul Bautista" class="identity__body-right-image">
        </div>
    </div>
</template>

<style scoped></style>
