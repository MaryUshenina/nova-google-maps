<template>
    <default-field :field="field" :errors="errors">
        <template slot="field">

            <gmap-autocomplete
                    :value="address"
                    :placeholder="field.placeholder || 'Find a location'"
                    class="w-full form-control form-input form-input-bordered"
                    :class="errorClasses"
                    :select-first-on-enter="true"
                    @place_changed="setPlace"
                    @input="value = $event.target.value"
                    :options="{}">
            </gmap-autocomplete>

            <gmap-map
                    :center="position"
                    :zoom="field.zoom"
                    @click="setPosition"
                    style="height: 400px"
                    class="mt-3"
            >

                <gmap-marker v-if="mark_exists"
                        :position="position"
                        :draggable="true"
                        @dragend="setPosition"
                ></gmap-marker>

            </gmap-map>

            <span @click="clearMarker" style="cursor:pointer">[х] Clear marker</span>
        </template>
    </default-field>
</template>

<script>
import { FormField, HandlesValidationErrors } from 'laravel-nova'

export default {
    mixins: [FormField, HandlesValidationErrors],

    props: ['resourceName', 'resourceId', 'field'],

    data() {
        return {
            mark_exists: false,
            address: null,
            latitude: null,
            longitude: null
        }
    },

    mounted: function() {
        console.log('Mounted branch');
    },

    computed: {
        latitudeAttr() {
            return this.field.latitude_attr;
        },
        longitudeAttr() {
            return this.field.longitude_attr;
        },
        position() {
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
        setInitialValue() {
            this.mark_exists  = this.field.mark_exists;

            this.latitude  = this.field.latitude;
            this.longitude = this.field.longitude;
        },

        /**
         * Fill the given FormData object with the field's internal value.
         */
        fill(formData) {
            if(this.mark_exists) {
                formData.append(this.latitudeAttr, this.latitude);
                formData.append(this.longitudeAttr, this.longitude);
            }
        },

        setPlace(place) {
            this.address   = place.formatted_address;
            this.latitude  = place.geometry.location.lat();
            this.longitude = place.geometry.location.lng();
        },

        setPosition(position) {
            this.mark_exists  = true;

            this.latitude  = position.latLng.lat();
            this.longitude = position.latLng.lng();
        },

        clearMarker(){
            this.mark_exists  = false;
        },
    },
}
</script>
