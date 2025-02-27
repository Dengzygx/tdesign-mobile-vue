<template>
  <div :class="classes">
    <div :class="`${name}__thumb`">
      <template v-if="imageContent">
        <t-image v-if="typeof image === 'string'" v-bind="customImageProps"></t-image>
        <t-node v-else :content="imageContent"></t-node>
      </template>
      <template v-else>
        <t-node v-if="iconContent !== undefined" :class="`${name}__icon`" :content="iconContent"></t-node>
      </template>
    </div>
    <div v-if="titleContent" :class="`${name}__title`">
      <t-node :content="titleContent"></t-node>
    </div>
    <div v-if="descriptionContent" :class="`${name}__description`">
      <t-node :content="descriptionContent"></t-node>
    </div>
  </div>
</template>

<script lang="ts">
import { h, computed, toRefs, getCurrentInstance, SetupContext, defineComponent } from 'vue';
import { InfoCircleIcon, CheckCircleIcon, CloseCircleIcon } from 'tdesign-icons-vue-next';
import resultProps from './props';
import config from '../config';
import { TdResultProps } from './type';
import { useEmitEvent, renderContent, renderTNode, TNode } from '../shared';

const { prefix } = config;
const name = `${prefix}-result`;

const iconDefault = {
  default: h(InfoCircleIcon),
  success: h(CheckCircleIcon),
  warning: h(InfoCircleIcon),
  error: h(CloseCircleIcon),
};

export default defineComponent({
  name,
  components: {
    TNode,
  },
  props: resultProps,
  setup(props) {
    const internalInstance = getCurrentInstance();
    const imageContent = computed(() => renderTNode(internalInstance, 'image'));
    const titleContent = computed(() => renderTNode(internalInstance, 'title'));
    const descriptionContent = computed(() => renderTNode(internalInstance, 'description'));

    const classes = computed(() => [`${name}`, `${name}--theme-${props.theme}`]);

    let iconContent: any;
    if (props.icon !== '') {
      if (Object.keys(iconDefault).includes(props?.theme as string)) {
        const key = props.theme as string;
        iconContent = computed(() => iconDefault?.[key]);
      }
      iconContent = props.icon ? computed(() => renderTNode(internalInstance, 'icon')) : iconContent;
    }

    const baseImageProps = {
      src: props.image,
    };

    const customImageProps = computed(() => ({
      ...props.imageProps,
      ...baseImageProps,
    }));

    return {
      name,
      classes,
      imageContent,
      customImageProps,
      iconContent,
      titleContent,
      descriptionContent,
      ...toRefs(props),
    };
  },
});
</script>
