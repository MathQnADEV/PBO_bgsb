<template>
    <q-page class="q-pa-md">
        <div class="q-mx-xl" style="max-width: 1300px; margin: auto; margin-top: 13%;">
            <q-card flat class="row q-gutter">
                <q-card-section class="col text-center">
                    <p v-if="profile" v-html="profile.biodata" class="text-left" ></p>
                </q-card-section>
                <div class="col text-center">
                    <img
                    v-if="profile"
                        style="width: 70%"
                        :src="/storage/ + profile.gambar"
                        alt="tau"
                    />
                </div>
            </q-card>
            <div v-if="$page.props.auth.user">
                <q-btn
                v-if="profile"
                label="edit Biodata"
                color="primary"
                class="q-mx-xs q-mt-xl"
                @click="edit"
                />
                <q-btn
                v-else
                    label="tambah Biodata"
                    color="primary"
                    class="q-mx-xs q-mt-xl"
                    @click="edit"
                />
            </div>
        </div>
        <q-dialog
            v-model="dialogProfile"
            transition-show="jump-right"
            transition-hide="jump-left"
        >
            <q-card style="width: 700px">
                <q-toolbar>
                    <q-toolbar-title>Upload Biodata</q-toolbar-title>
                    <q-space />
                    <q-btn @click="batal" round flat icon="cancel" />
                </q-toolbar>
                <q-card-section>
                    <q-form class="q-gutter-sm">
                        <q-editor v-model="form.biodata" min-height="10rem" />
                        <q-file
                            class="q-mb-sm"
                            outlined
                            label="Upload img"
                            v-model="filex.gambar"
                            @input="filex.gambar = $event.target.files[0]"
                        />
                        <div class="text-right">
                            <q-btn @click="simpan" label="simpan" />
                            <q-btn
                                @click="batal"
                                label="batal"
                                color="teal"
                                class="q-mx-sm"
                            />
                        </div>
                    </q-form>
                    <q-form></q-form>
                </q-card-section>
            </q-card>
        </q-dialog>
    </q-page>
</template>

<script>
import layout from "../layout/layout.vue";
import { reactive, ref } from "vue";
import { router, useForm } from "@inertiajs/vue3";
import axios from "axios";
export default {
    layout: layout,

    props: {
        profile: Array,
    },

    setup() {
        const dialogProfile = ref(false);

        const filex = useForm({
            gambar: null,
        });

        const form = reactive({
            biodata: "",
        });

        function batal() {
            form.biodata = "";
            filex.gambar = null;
            dialogProfile.value = false;
        }

        function edit() {
            axios.get("/profile/edit").then((res) => {
                form.biodata = res.data.biodata;
                dialogProfile.value = true;
            });
        }

        function simpan() {
            router.visit("/user/profileSimpan", {
                method: "post",
                data: {
                    biodata: form.biodata,
                    gambar: filex.gambar,
                },
                preserveScroll: true,
                forceFormData: true,
                preserveState: true,
                onSuccess: () => {
                    batal();
                },
            });
        }

        return {
            dialogProfile,
            filex,
            form,
            // editDialog,
            edit,
            batal,
            simpan,
        };
    },
};
</script>

<style>
</style>
