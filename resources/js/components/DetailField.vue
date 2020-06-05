<template>
    <panel-item :field="field">
        <template slot="value">
            <gmap-map
                :center="position"
                :zoom="field.zoom"
                style="height: 400px"
                class="mt-3"
            >
                <gmap-marker
                    :position="position"
                ></gmap-marker>

            </gmap-map>
        </template>
    </panel-item>
</template>

<script>

    import { FormField, HandlesValidationErrors } from 'laravel-nova'

    export default {
        mixins: [FormField, HandlesValidationErrors],

        props: ['resource', 'resourceName', 'resourceId', 'field'],

        data () {
            return {
                address: null,
                latitude: null,
                longitude: null
            }
        },

        computed: {
            latitudeAttr () {
                return this.field.latitude_attr
            },
            longitudeAttr () {
                return this.field.longitude_attr
            },
            position () {
                return {
                    lat: this.latitude,
                    lng: this.longitude
                }
            }
        },

        methods: {

            /**
             * Set the initial value for the field
             */
            setInitialValue () {
                this.latitude = this.field.latitude
                this.longitude = this.field.longitude
            },

            setPlace (place) {
                this.address = place.formatted_address
                this.latitude = place.geometry.location.lat()
                this.longitude = place.geometry.location.lng()
            },

            setPosition (position) {
                this.latitude = position.latLng.lat()
                this.longitude = position.latLng.lng()
            },
        }

    }
</script>
