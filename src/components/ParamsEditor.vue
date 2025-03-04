<template>
  <div>
    <div class="input-wrapper-title">
      <!-- 参数编辑 -->
      <div class="title-label">
        <nut-switch v-model="showKeyValueLocal" />
        <span>{{ titleEditText }}</span>
      </div>
      <!-- 本地脚本没有noCache -->
      <div v-if="mode === 'link'" class="title-label">
        <nut-switch v-model="noCacheLocal" />
        <span>{{ noCacheText }}</span>
        <font-awesome-icon
          class="icon"
          icon="fa-solid fa-circle-question"
          @click.stop="showNoCacheTips"
        />
      </div>
      <!-- 添加参数 -->
      <div v-if="showKeyValueLocal" class="button">
        <div @click="addParameter">{{ addParamsText }}</div>
      </div>
    </div>
    <div v-if="showKeyValueLocal" class="key-value-container">
      <div class="key-value-box">
        <div class="header">
          <div class="item">key</div>
          <div class="item">value</div>
          <div class="item">{{ optionsText }}</div>
        </div>
        <div class="content">
          <div
            v-for="(item, index) in paramsArgumentsLocal"
            :key="index"
            class="key-value-row"
          >
            <div class="item">
              <nut-textarea
                v-model="item.key"
                :border="false"
                placeholder="key"
                type="text"
                :rows="1"
                autosize
                @blur="trimValue(item, 'key')"
              />
            </div>
            <div class="item">
              <nut-textarea
                v-model="item.value"
                :border="false"
                placeholder="value"
                type="text"
                :rows="1"
                autosize
                @blur="trimValue(item, 'value')"
              />
            </div>
            <div class="item key-value-operation">
              <div @click="deleteItem(index)">{{ deleteParamsText }}</div>
            </div>
          </div>
          <div v-if="!paramsArgumentsLocal.length" class="empty-tips">
            <p>{{ emptyTipsText }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Dialog } from "@nutui/nutui";
import { computed } from "vue";
import { useI18n } from "vue-i18n";

const props = defineProps({
  paramsArguments: {
    type: Array,
    default: () => [],
  },
  showKeyValue: {
    type: Boolean,
    default: false,
  },
  noCache: {
    type: Boolean,
    default: false,
  },
  type: {
    type: String,
    required: true,
  },
  mode: {
    type: String,
    default: "link",
  },
});

const emit = defineEmits([
  "update:paramsArguments",
  "update:showKeyValue",
  "update:noCache",
]);

const { t } = useI18n();

const showKeyValueLocal = computed({
  get: () => props.showKeyValue,
  set: (val) => emit("update:showKeyValue", val),
});

const noCacheLocal = computed({
  get: () => props.noCache,
  set: (val) => emit("update:noCache", val),
});

const paramsArgumentsLocal = computed({
  get: () => props.paramsArguments,
  set: (val) => emit("update:paramsArguments", val),
});

// 文本内容通过i18n翻译
const titleEditText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].paramsEdit`),
);
const noCacheText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].noCache`),
);
const addParamsText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].paramsAdd`),
);
const optionsText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].paramsOptions`),
);
const deleteParamsText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].paramsDelete`),
);
const emptyTipsText = computed(() =>
  t(`editorPage.subConfig.nodeActions['${props.type}'].paramsEmpty`),
);

const showNoCacheTips = () => {
  Dialog({
    title: t(`editorPage.subConfig.nodeActions['${props.type}'].helpTitle`),
    content: t(`editorPage.subConfig.nodeActions['${props.type}'].noCacheTips`),
    popClass: "auto-dialog",
    okText: "OK",
    noCancelBtn: true,
    // @ts-expect-error
    closeOnClickOverlay: true,
  });
};

const addParameter = () => {
  const newParamsArguments = [...props.paramsArguments, { key: "", value: "" }];
  emit("update:paramsArguments", newParamsArguments);
};

const deleteItem = (index) => {
  const newParamsArguments = [...props.paramsArguments];
  newParamsArguments.splice(index, 1);
  emit("update:paramsArguments", newParamsArguments);
};

const trimValue = (item, field) => {
  if (item[field] && typeof item[field] === "string") {
    item[field] = item[field].replace(/\s+/g, "");
  }
};
</script>

<style lang="scss" scoped>
.input-wrapper-title {
  display: flex;
  align-items: center;
  margin-top: 16px;
  margin-bottom: 8px;
  .title-label {
    display: flex;
    align-items: center;
    font-size: 14px;
    color: var(--second-text-color);
    padding-left: 8px;
    .icon {
      margin-left: 4px;
      color: var(--unimportant-icon-color);
    }
  }
  span {
    font-size: 12px;
    color: var(--second-text-color);
    padding-left: 8px;
  }
  .button {
    margin-left: auto;
    div {
      cursor: pointer;
      color: var(--primary-color);
      margin: 0 8px;
      font-size: 12px;
    }
  }
}
.key-value-container {
  .key-value-box {
    .header {
      display: grid;
      grid-template-columns: 2fr 2fr 1fr;
      font-size: 14px;
      color: var(--second-text-color);
      padding: 8px 0;
      border-bottom: 1px solid var(--lowest-text-color);
      .item {
        text-align: left;
        padding: 8px 12px;
        &:last-child {
          text-align: center;
        }
      }
    }
    .content {
      .key-value-row {
        display: grid;
        grid-template-columns: 2fr 2fr 1fr;
        padding: 8px 0;
        .item {
          text-align: left;
          display: flex;
          align-items: center;

          :deep(.nut-textarea) {
            width: 100%;
            background: transparent;
            padding: 8px 12px;
            color: var(--second-text-color);

            :deep(.nut-textarea__textarea) {
              color: inherit;
              min-height: unset;
              height: auto;
              overflow: hidden;
            }
          }

          &.key-value-operation {
            display: flex;
            align-items: center;
            justify-content: center;
            div {
              cursor: pointer;
              color: var(--primary-color);
              margin: 0 8px;
            }
          }
        }
      }
      .empty-tips {
        display: flex;
        justify-content: center;
        padding: 8px 0;
        color: var(--comment-text-color);
        p {
          font-size: 12px;
        }
      }
    }
  }
}
</style>
