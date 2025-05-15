<script setup>
import { ref, computed, reactive, inject } from "vue";
import * as yup from "yup";
import moment from "moment";

const translations = inject('translations', {});
const policeLogo = inject('policeLogo', '');
const firstname = ref("");
const lastname = ref("");
const dob = ref("");
const height = ref(180);
const gender = ref("m");

const errors = reactive({
    firstname: "",
    lastname: "",
    dob: "",
    height: "",
    gender: "",
});

const validateField = (field, value) => {
    try {
        getError(field).validateSync(value);
        errors[field] = "";
        return true;
    } catch (error) {
        errors[field] = error.message;
        return false;
    }
};

const onSubmit = () => {
    const isFirstnameValid = validateField("firstname", firstname.value);
    const isLastnameValid = validateField("lastname", lastname.value);
    const isDobValid = validateField("dob", dob.value);
    const isHeightValid = validateField("height", height.value);
    const isGenderValid = validateField("gender", gender.value);

    if (isFirstnameValid && isLastnameValid && isDobValid && isHeightValid && isGenderValid) {
        fetch("http://esx_identity/register", {
            method: "POST",
            body: JSON.stringify({
                firstname: firstname.value,
                lastname: lastname.value,
                dateofbirth: moment(dob.value).format("DD/MM/YYYY"),
                sex: gender.value,
                height: height.value,
            }),
        });
    }
};

const getError = (field) => {
    const validations = {
        firstname: yup
            .string()
            .required(translations.value?.firstname_required || "Le prénom est requis")
            .min(3, translations.value?.firstname_min || "Le prénom doit contenir au moins 3 caractères"),
        lastname: yup
            .string()
            .required(translations.value?.lastname_required || "Le nom est requis")
            .min(3, translations.value?.lastname_min || "Le nom doit contenir au moins 3 caractères"),
        dob: yup
            .date()
            .required(translations.value?.dob_required || "La date de naissance est requise")
            .nullable()
            .typeError(translations.value?.dob_invalid || "La date est invalide")
            .min(new Date("1900-01-01"), translations.value?.dob_too_old || "Date trop ancienne")
            .max(moment().subtract(1, "years").toDate(), translations.value?.dob_too_young || "Vous devez avoir au moins 1 an"),
        gender: yup.string().required(translations.value?.gender_required || "Le genre est requis"),
        height: yup
            .number()
            .required(translations.value?.height_required || "La taille est requise")
            .min(120, translations.value?.height_min || "La taille minimum est 120")
            .max(220, translations.value?.height_max || "La taille maximum est 220")
            .typeError(translations.value?.height_invalid || "La taille doit être un nombre"),
    };

    return validations[field];
};

const isFormValid = computed(() => {
    validateField("firstname", firstname.value);
    validateField("lastname", lastname.value);
    validateField("dob", dob.value);
    validateField("height", height.value);
    validateField("gender", gender.value);
    return Object.values(errors).every((error) => error === "");
});

const formattedDob = computed(() => {
    if (!dob.value) return "";
    return moment(dob.value).format("YYYY-MM-DD");
});

const age = computed(() => {
    if (!dob.value) return "";
    return moment().diff(moment(dob.value), "years");
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
        return translations.value?.male || "Homme";
    } else {
        return translations.value?.female || "Femme";
    }
};

const formatTextLong = (text) => {
    return text.length > 9 ? text.substring(0, 9) + "..." : text;
};

const rangeBackgroundSize = computed(() => {
    const percentage = ((height.value - 140) / (200 - 140)) * 100;
    return `${percentage}% 100%`;
});
</script>

<template>
    <div class="identity">
        <div class="identity__header">
            <h1>{{ translations.title || "CHARACTER" }} <span>{{ translations.subtitle || "IDENTITY" }}</span></h1>
            <p>
                {{ translations.description || "Lorem Ipsum is simply dummy text of the printing and typesetting industry. typesetting industry. typesetting industry.typesetting industry." }}
            </p>
        </div>
        <div class="identity__body">
            <div class="identity__body-left">
                <div class="identity__body-left-police">
                    <div
                        class="identity__body-left-police-image"
                        :style="{ backgroundImage: `url(${policeLogo})` }"
                    ></div>
                    <h1>{{ translations.police_title || "Police Department" }}</h1>
                    <p>{{ translations.police_description || "Join the force and help keep the city safe" }}</p>
                </div>

                <div class="identity__body-left-card">
                    <div
                        class="identity__body-left-card-photo"
                        :style="{ backgroundImage: `url(${getAvatar()})` }"
                    ></div>
                    <div class="identity__body-left-card-info">
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">{{ translations.firstname_label || "PRÉNOM" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    formatTextLong(firstname) || "---"
                                }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">{{ translations.lastname_label || "NOM" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    formatTextLong(lastname) || "---"
                                }}</span>
                            </div>
                        </div>
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">{{ translations.dob_label || "DATE DE NAISSANCE" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    formattedDob || "---"
                                }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">{{ translations.gender_label || "SEXE" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    getGenderLabel(gender)
                                }}</span>
                            </div>
                        </div>
                        <div class="identity__body-left-card-info-row">
                            <div class="identity__body-left-card-info-column first">
                                <span class="identity__body-left-card-info-label">{{ translations.age_label || "ÂGE" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    age || "---"
                                }}</span>
                            </div>
                            <div class="identity__body-left-card-info-column">
                                <span class="identity__body-left-card-info-label">{{ translations.height_label || "TAILLE" }}</span>
                                <span class="identity__body-left-card-info-value">{{
                                    height
                                }}</span>
                            </div>
                        </div>
                    </div>
                    <div
                        class="identity__body-left-card-seal"
                        style="
                            background-image: url('https://fivem.lorisdev.fr/images/esx_identity/policeseal.jpg');
                        "
                    >
                        <div class="identity__body-left-card-seal-image"></div>
                    </div>
                </div>
            </div>

            <div class="identity__body-center">
                <form @submit.prevent="onSubmit" class="identity__body-center-form">
                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="firstName">{{ translations.firstname_label || "PRÉNOM" }}</label>
                                <input
                                    id="firstname"
                                    type="text"
                                    name="firstname"
                                    :placeholder="translations.firstname_placeholder || 'Jason'"
                                    @input="validateField('firstname', firstname)"
                                    v-model="firstname"
                                />
                            </div>
                            <i class="fa-duotone fa-solid fa-address-card"></i>
                        </div>
                        <div class="identity__body-center-form-group-error" v-if="errors.firstname">
                            <div class="identity__body-center-form-group-error-icons">
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div class="identity__body-center-form-group-error-icons-icon">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>{{ translations.error_title || "Error" }}</h1>
                                <span class="error-message">{{ errors.firstname }}</span>
                            </div>
                        </div>
                    </div>

                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="lastName">{{ translations.lastname_label || "NOM" }}</label>
                                <input
                                    id="lastname"
                                    type="text"
                                    name="lastname"
                                    :placeholder="translations.lastname_placeholder || 'Duval'"
                                    @input="validateField('lastname', lastname)"
                                    v-model="lastname"
                                />
                            </div>
                            <i class="fa-duotone fa-solid fa-address-card"></i>
                        </div>
                        <div class="identity__body-center-form-group-error" v-if="errors.lastname">
                            <div class="identity__body-center-form-group-error-icons">
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div class="identity__body-center-form-group-error-icons-icon">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>{{ translations.error_title || "Error" }}</h1>
                                <span class="error-message">{{ errors.lastname }}</span>
                            </div>
                        </div>
                    </div>

                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="dob">{{ translations.dob_label || "DATE DE NAISSANCE" }}</label>
                                <input
                                    id="dob"
                                    type="date"
                                    name="dob"
                                    :placeholder="translations.dob_placeholder || 'DD/MM/YYYY'"
                                    @input="validateField('dob', dob)"
                                    v-model="dob"
                                />
                            </div>
                            <i class="fa-duotone fa-solid fa-cake-candles"></i>
                        </div>
                        <div class="identity__body-center-form-group-error" v-if="errors.dob">
                            <div class="identity__body-center-form-group-error-icons">
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div class="identity__body-center-form-group-error-icons-icon">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>{{ translations.error_title || "Error" }}</h1>
                                <span class="error-message">{{ errors.dob }}</span>
                            </div>
                        </div>
                    </div>

                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="height">{{ translations.height_label || "TAILLE" }}</label>
                                <div class="identity__body-center-form-group-input-label-range">
                                    <input
                                        id="height"
                                        type="range"
                                        name="height"
                                        min="140"
                                        max="200"
                                        @input="validateField('height', height)"
                                        v-model="height"
                                        :style="{ backgroundSize: rangeBackgroundSize }"
                                    />
                                    <span
                                        class="identity__body-center-form-group-input-label-range-value"
                                        >{{ height }}</span
                                    >
                                </div>
                            </div>
                            <i class="fa-duotone fa-solid fa-line-height"></i>
                        </div>
                        <div class="identity__body-center-form-group-error" v-if="errors.height">
                            <div class="identity__body-center-form-group-error-icons">
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div class="identity__body-center-form-group-error-icons-icon">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>{{ translations.error_title || "Error" }}</h1>
                                <span class="error-message">{{ errors.height }}</span>
                            </div>
                        </div>
                    </div>

                    <div class="identity__body-center-form-group">
                        <div class="identity__body-center-form-group-input">
                            <div class="identity__body-center-form-group-input-label">
                                <label for="gender">{{ translations.gender_label || "SEXE" }}</label>
                                <div class="identity__body-center-form-group-input-label-radio">
                                    <input
                                        id="gender"
                                        type="radio"
                                        name="gender"
                                        value="m"
                                        @change="validateField('gender', gender)"
                                        v-model="gender"
                                        class="man"
                                    />
                                    <input
                                        id="female"
                                        type="radio"
                                        name="gender"
                                        value="f"
                                        @change="validateField('gender', gender)"
                                        v-model="gender"
                                        class="woman"
                                    />
                                </div>
                            </div>
                            <i class="fa-duotone fa-solid fa-venus-mars"></i>
                        </div>
                        <div class="identity__body-center-form-group-error" v-if="errors.gender">
                            <div class="identity__body-center-form-group-error-icons">
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div class="identity__body-center-form-group-error-icons-icon">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color1)' }"
                                ></div>
                                <div
                                    class="identity__body-center-form-group-error-icons-bar"
                                    :style="{ backgroundColor: 'var(--light-color2)' }"
                                ></div>
                            </div>
                            <div class="identity__body-center-form-group-error-message">
                                <h1>{{ translations.error_title || "Error" }}</h1>
                                <span class="error-message">{{ errors.gender }}</span>
                            </div>
                        </div>
                    </div>

                    <button
                        type="submit"
                        class="identity__body-center-form-submit"
                        :class="{ disabled: !isFormValid }"
                    >
                        {{ translations.submit_button || "CRÉER PERSONNAGE" }}
                    </button>
                </form>
            </div>

            <img
                src="https://fivem.lorisdev.fr/images/esx_identity/Raul_Bautista.webp"
                alt="Raul Bautista"
                class="identity__body-right-image"
            />

            <div class="identity__body-fake"></div>
        </div>
    </div>
</template>

<style scoped></style>
