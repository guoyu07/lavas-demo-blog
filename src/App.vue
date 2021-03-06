<template>
    <div id="app">
        <div class="app-shell app-shell-bottom-navigation">
            <app-header
                class="app-shell-header"
                @click-menu="handleClickHeaderMenu"
                @click-back="handleClickHeaderBack">
                <template slot="logo"></template>
            </app-header>
            <app-sidebar
                @hide-sidebar = "handleHideSidebar"
                @show-sidebar = "handleShowSidebar"
            >
                <template slot="logo"><span></span></template>
            </app-sidebar>
            <div class="app-view-wrapper">
                <transition
                    :name="pageTransitionName"
                    @before-enter="handleBeforeEnter"
                    @after-enter="handleAfterEnter">
                    <keep-alive>
                        <router-view
                            v-if="!$route.meta.notKeepAlive"
                            class="app-view"
                            :class="{
                                'app-view-with-header': appHeader.show
                            }"></router-view>
                    </keep-alive>
                </transition>
                <transition
                    :name="pageTransitionName"
                    @before-enter="handleBeforeEnter"
                    @after-enter="handleAfterEnter">
                    <router-view
                        v-if="$route.meta.notKeepAlive"
                        class="app-view"
                        :class="{
                            'app-view-with-header': appHeader.show
                        }"></router-view>
                </transition>
            </div>
        </div>
    </div>
</template>

<script>
import {mapState, mapActions} from 'vuex';
import AppHeader from '@/components/AppHeader';
import AppSidebar from '@/components/AppSidebar';

export default {
    name: 'app',
    components: {
        AppHeader,
        AppSidebar
    },
    data() {
        return {};
    },
    computed: {
        ...mapState('appShell', [
            'appHeader',
            'pageTransitionName'
        ])
    },
    methods: {
        ...mapActions('appShell', [
            'setPageSwitching'
        ]),
        ...mapActions('appShell/appSidebar', [
            'showSidebar',
            'hideSidebar'
        ]),
        handleBeforeEnter() {
            this.setPageSwitching(true);
        },
        handleAfterEnter() {
            this.setPageSwitching(false);
        },
        handleClickHeaderBack() {
            this.$router.go(-1);
        },
        handleClickHeaderMenu() {
            this.showSidebar();
        },
        handleHideSidebar() {
            this.hideSidebar();
        },
        handleShowSidebar() {
            this.showSidebar();
        }
    }
};
</script>

<style lang="stylus">
#app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    color #2c3e50
</style>

<style lang="stylus" scoped>
.app-shell
    position absolute
    top 0
    right 0
    bottom 0
    left 0
    width 100%
    height 100%
    display flex
    flex-direction column

    .app-shell-header
        position fixed
        top 0
        left 0
        right 0

    .app-shell-footer
        position fixed
        bottom 0
        left 0
        right 0

    .app-view-wrapper
        flex 1
        position relative

        .app-view-loading
            position fixed
            top 50%
            left 50%
            transform translate(-50%, -50%)
            z-index 100
            color: $theme.primary
        .app-view
            position absolute
            width 100%
            margin-top 0
            transition transform 0.4s cubic-bezier(.55, 0, .1, 1)
            background: $material-theme.bg-color
            color: $material-theme.text-color

            &.app-view-with-header
                top $app-header-height

            &.app-view-with-footer
                bottom $app-footer-height

            &.slide-left-enter
                transform translate(100%, 0)

            &.slide-right-enter
                transform translate(-100%, 0)

            &.slide-right-leave-active
                transform translate(100%, 0)

            &.slide-left-leave-active
                transform translate(-100%, 0)
</style>
