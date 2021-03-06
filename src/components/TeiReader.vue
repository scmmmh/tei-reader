<template>
    <div class="tei-reader">
        <div :class="this.$store.state.ui.smallMenuOpen ? 'small-menu-open' : null">
            <nav v-if="isSmall && isSmallMenuOpen" class="main">
                <ul>
                    <li><a @click="toggleSmallMenu">&#x2630;</a></li>
                </ul>
            </nav>
            <nav v-if="(!isSmall && hasMultipleSections) || (isSmall && isSmallMenuOpen)" class="main">
                <ul role="menubar">
                    <li v-for="value, key in sections" :key="key" role="presentation">
                        <a role="menuitem" v-html="value.label" @click="selectSection(key)" :aria-checked="key === selectedSection ? 'true' : 'false'"></a>
                    </li>
                    <li v-if="closeLabel && closeCallback" role="presentation">
                        <a role="menuitem" v-html="closeLabel" @click="close"></a>
                    </li>
                </ul>
            </nav>
        </div>
        <template v-for="value, key in sections">
            <text-reader v-if="key === selectedSection && value.type === 'Text'" :key="key" :section="key"></text-reader>
            <metadata-reader v-else-if="key === selectedSection && value.type === 'Metadata'" :key="key" :section="key"></metadata-reader>
            <nested-list-reader v-else-if="key === selectedSection && value.type === 'NestedList'" :key="key" :section="key"></nested-list-reader>
        </template>
    </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import TextReader from './TextReader.vue';
import MetadataReader from './MetadataReader.vue';
import NestedListReader from './NestedListReader.vue';

@Component({
    components: {
        TextReader,
        MetadataReader,
        NestedListReader,
    },
})
export default class TeiReader extends Vue {
    public get isSmall() {
        return this.$store.state.ui.mode === 'small';
    }

    public get isSmallMenuOpen() {
        return this.$store.state.ui.smallMenuOpen;
    }

    public get hasMultipleSections() {
        return Object.keys(this.$store.state.sections).length > 1;
    }

    public get sections() {
        return this.$store.state.sections;
    }

    public get selectedSection() {
        return this.$store.state.ui.selectedSection;
    }


    public get closeLabel() {
        return this.$store.state.ui.closeLabel;
    }

    public get closeCallback() {
        return this.$store.state.callbacks.close;
    }

    public selectSection(key: string) {
        this.$store.commit('selectSection', key);
        if (this.isSmall) {
            this.$store.commit('toggleSmallMenu');
        }
    }

    public toggleSmallMenu() {
        this.$store.commit('toggleSmallMenu');
    }

    public close() {
        if (this.isSmall) {
            this.$store.commit('toggleSmallMenu');
        }
        this.closeCallback();
    }
}
</script>
