<template>
    <div>
        <div class="row no-gutters">
            <div :class="itemClass" v-for="(photo, index) in photos" v-bind:key="photo.id" >
                <div class="photo-tile has-text-centered">
                    <div class="embed-responsive embed-responsive-16by9">
                        <img @click="openViewer(index)" class="embed-responsive-item cursor-pointer" style="height:auto;" :id="'photo-' + photo.id" :src="photo.url" :alt="photo.name" >
                    </div>
                </div>
            </div>
        </div>
        <portal to="modal" >
            <div class="modal-frame"  v-if="galleryIsOpen" >
                <div class="modal-container">
                    <div v-if="photoOpen" class="photo-container" style="height: 80vh; overflow:hidden; padding:1rem;" @click="closeViewer()" >
                        <img :src="activePhoto.url" :alt="activePhoto.name" style="max-width:100%;max-height:100%;" />
                    </div>
                    <div v-if="tourOpen" class="photo-container" style="height: 80vh; width:100%; overflow:hidden; padding:1rem;" @click="closeViewer()" >
                        <iframe :src="'//' + virtualTour" height="100%" width="100%" border="0" style="overflow:hidden;" ></iframe>
                    </div>
                    <div v-if="hasVirtualTour" class="text-xs-center" style="height: 7vh;" >
                        <a class="btn btn-primary pointer" v-if="!tourOpen" style="color:#FFF;" @click="openTour" >Open Virtual Tour</a>
                        <a class="btn btn-primary pointer" v-if="tourOpen" style="color:#FFF;" @click="closeTour" >Close Virtual Tour</a>
                        <a class="btn btn-primary pointer" v-if="tourOpen" style="color:#FFF;" :src="'//' + virtualTour" target="_blank" >Open in new Tab</a>
                    </div>
                    <div class="text-xs-center" style="height: 7vh;" >
                        <a class="dirbutton pointer" style="margin: 0 1rem;" @click="prevPhoto(activePhoto.index);">
                            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
                                viewBox="0 0 306 306" style="transform: rotate(180deg); height:30px; enable-background:new 0 0 306 306;"  xml:space="preserve">
                                <polygon fill="#FFF" points="94.35,0 58.65,35.7 175.95,153 58.65,270.3 94.35,306 247.35,153"></polygon>
                            </svg>
                        </a>
                        
                        <a @click="closeViewer" class="btn btn-info pointer">close</a>

                        <a class="dirbutton pointer" style="margin: 0 1rem;" @click="nextPhoto(activePhoto.index);">
                            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
                                viewBox="0 0 306 306" style="height:30px; enable-background:new 0 0 306 306;"  xml:space="preserve">
                                <polygon fill="#FFF" points="94.35,0 58.65,35.7 175.95,153 58.65,270.3 94.35,306 247.35,153"></polygon>
                            </svg>
                        </a>
                    </div>
                </div>
            </div>
        </portal>
    </div>
</template>

<script>
    export default {
        props: {
            dataPhotos: {
                type: Array,
                default: () => []
            },
            virtualTour: {
                type: String,
                default: null
            },
            itemClass: {
                type: String,
                default: ''
            },
            viewerState: {
                type: Boolean,
                default: false
            }
        },
        data () {
            return {
                photos: [],
                prev: null,
                next: null,
                activePhoto: {},
                numPhotos: 0,
                hasVirtualTour: false,
                tourOpen: false,
                photoOpen: true
            }
        },
        mounted () {
            this.photos = this.dataPhotos;
            this.numPhotos = this.photos.length;
            this.activePhoto = this.photos[0];
            this.activePhoto.index = 0;
            
            if(this.virtualTour != null){
                this.hasVirtualTour = true;
            }

            if(viewerState == true){
                this.openViewer(0);
            }
            
        },
        computed: {
            galleryIsOpen: {
                get: function () {
                    return this.viewerState;
                },
                set: function (newValue) {
                    return this.viewerState;
                }
            }
        },

        methods: {
            openViewer(index){
                this.$emit('openviewer');

                this.activePhoto = this.photos[index];
                this.activePhoto.index = index;
            },
            openTour(){
                this.tourOpen = true;
                this.photoOpen = false;
            },
            closeTour(){
                this.tourOpen = false;
                this.photoOpen = true;
            },
            closeViewer(){
                this.$emit('closeviewer');
            },
            nextPhoto(index){
                let newNum = (index !== this.numPhotos-1 ? index+1 : 0);
                this.activePhoto = this.photos[newNum];
                this.activePhoto.index = newNum;
                this.closeTour();
            },
            prevPhoto(index){
                let newNum = (index !== 0 ? index-1 : this.numPhotos-1);
                this.activePhoto = this.photos[newNum];
                this.activePhoto.index = newNum;
                this.closeTour();
            }
        }
    }
</script>

<style scoped>
    .photo-tile {
        overflow: hidden;
    }
    .photo-tile img {
        cursor: pointer;
    }
    .modal-frame {
        z-index: 999999999999999;
        height: 100%;
        width:100%;
        background-color: rgba(0, 0, 0, 0.9);
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        position: fixed;
    }
    .modal-container {
        padding: 1rem;
        display: flex;
        height: 100%;
        width:100%;
        align-items: center;
        flex-direction: column;
        justify-content: space-between;
    }
    .dirbutton {
        display: inline-block;
        line-height: 1em;
        vertical-align: middle;
        margin:0 1rem;
    }
    .photo-container {
        height: 80vh;
        overflow: hidden;
        align-items: center;
        display: flex;
        padding: 1rem;
        flex-direction: column;
        justify-content: center;
    }
</style>