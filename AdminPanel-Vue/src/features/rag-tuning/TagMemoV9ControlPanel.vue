<template>
  <section class="v9-console">
    <header class="v9-console__header">
      <div class="v9-console__identity">
        <span class="material-symbols-outlined">experiment</span>
        <div>
          <div class="v9-console__eyebrow">TagMemo Dual Track Laboratory</div>
          <h3>V8.3 / V9 双轨实验控制台</h3>
          <p>
            生产出口默认保持 V8.3；V9 在同一图事实底座上构建有界传播核，供 LightMemo
            A/B 寻址与灰度测试使用。
          </p>
        </div>
      </div>
      <div class="v9-console__status">
        <UiBadge :variant="activeVersion === 'v9' ? 'warning' : 'success'">
          生产：{{ activeVersionLabel }}
        </UiBadge>
        <UiBadge variant="outline">
          回退：{{ fallbackVersionLabel }}
        </UiBadge>
        <UiBadge :variant="dualArtifacts ? 'info' : 'danger'">
          双资产：{{ dualArtifacts ? "开启" : "关闭" }}
        </UiBadge>
      </div>
    </header>

    <div class="v9-console__warning">
      <span class="material-symbols-outlined">shield</span>
      <p>
        切换生产版本只会原子替换活动资产包引用，不会修改已发布 Map。显式 A/B
        请求建议始终保持严格版本模式，禁止缺失版本静默回退。
      </p>
    </div>

    <div class="v9-console__grid">
      <article class="v9-card v9-card--version">
        <header class="v9-card__header">
          <div>
            <span class="v9-card__kicker">Production Gate</span>
            <h4>版本出口与安全语义</h4>
          </div>
          <UiBadge variant="warning">保存后热切换</UiBadge>
        </header>

        <label class="v9-field">
          <span class="v9-field__label">生产活动版本</span>
          <UiSelect :model-value="activeVersion" @update:model-value="setVersionField('activeVersion', $event)">
            <option value="v8_3">V8.3 — 兼容生产路径</option>
            <option value="v9">V9 — 有界传播实验路径</option>
          </UiSelect>
          <small>决定普通未显式指定版本的查询使用哪套完整资产包。</small>
        </label>

        <label class="v9-field">
          <span class="v9-field__label">生产回退版本</span>
          <UiSelect :model-value="fallbackVersion" @update:model-value="setVersionField('fallbackVersion', $event)">
            <option value="v8_3">V8.3 — 推荐安全回退</option>
            <option value="v9">V9 — 仅开发环境建议</option>
          </UiSelect>
          <small>只有隐式生产请求允许回退；显式版本请求不会使用此项冒充成功。</small>
        </label>

        <div class="v9-toggle-list">
          <label class="v9-toggle">
            <input
              type="checkbox"
              :checked="dualArtifacts"
              @change="setVersionBoolean('dualArtifacts', $event)"
            />
            <span>
              <strong>并行构建双版本资产</strong>
              <small>同一 staging 周期构建 V8.3 与 V9，并一次性发布 registry。</small>
            </span>
          </label>

          <label class="v9-toggle">
            <input
              type="checkbox"
              :checked="strictExplicitVersion"
              @change="setVersionBoolean('strictExplicitVersion', $event)"
            />
            <span>
              <strong>显式版本严格失败</strong>
              <small>A/B 测试指定的版本不可用时直接报错，不允许回退污染实验。</small>
            </span>
          </label>
        </div>
      </article>

      <article class="v9-card v9-card--kernel">
        <header class="v9-card__header">
          <div>
            <span class="v9-card__kicker">Bounded Kernel</span>
            <h4>V9 有界传播与虫洞预算</h4>
          </div>
          <UiBadge variant="danger">高敏感</UiBadge>
        </header>

        <div class="v9-budget">
          <div>
            <span>单节点最大出流质量</span>
            <strong>{{ formatNumber(v9.outboundMass) }}</strong>
          </div>
          <div class="v9-budget__track">
            <span :style="{ width: `${clamp(v9.outboundMass, 0, 1) * 100}%` }"></span>
          </div>
          <small>归一化后每个节点所有出边传导率之和不超过该预算。</small>
        </div>

        <NumericField
          label="出流总质量"
          description="控制每跳最多保留多少传播质量；必须不大于 1。"
          :model-value="v9.outboundMass"
          :min="0.1"
          :max="1"
          :step="0.01"
          @update:model-value="setV9Number('outboundMass', $event)"
        />
        <NumericField
          label="共现证据压缩"
          description="进入 log1p 前对累计共现证据缩放，降低高频边对竞争的垄断。"
          :model-value="v9.evidenceCompression"
          :min="0.01"
          :max="5"
          :step="0.01"
          @update:model-value="setV9Number('evidenceCompression', $event)"
        />
        <NumericField
          label="虫洞竞争增益"
          description="只在归一化前提高虫洞边竞争力，不会在归一化后额外创造能量。"
          :model-value="v9.wormholeGain"
          :min="1"
          :max="3"
          :step="0.01"
          @update:model-value="setV9Number('wormholeGain', $event)"
        />
        <NumericField
          label="虫洞张力门槛"
          description="压缩后的边证据乘目标锚增益达到此值才标记为虫洞。"
          :model-value="v9.tensionThreshold"
          :min="0"
          :max="3"
          :step="0.01"
          @update:model-value="setV9Number('tensionThreshold', $event)"
        />
      </article>

      <article class="v9-card v9-card--residual">
        <header class="v9-card__header">
          <div>
            <span class="v9-card__kicker">Residual Instrument</span>
            <h4>内生残差与锚增益</h4>
          </div>
          <UiBadge variant="info">JS / Rust 单一真相源</UiBadge>
        </header>

        <label class="v9-field">
          <span class="v9-field__label">残差算法</span>
          <UiSelect :model-value="residual.method" @update:model-value="setResidualField('method', $event)">
            <option value="anchored_gs">Anchored Gram-Schmidt</option>
            <option value="centroid">Centroid Residual</option>
            <option value="svd">SVD Residual</option>
          </UiSelect>
          <small>实际生效配置由 JS 完整传入 Rust，并纳入 artifact signature。</small>
        </label>

        <label class="v9-toggle v9-toggle--standalone">
          <input
            type="checkbox"
            :checked="residual.semanticEnabled"
            @change="setResidualBoolean('semanticEnabled', $event)"
          />
          <span>
            <strong>启用 Pairwise 语义门控</strong>
            <small>使用当前模型签名下的成对余弦缓存调制残差邻域。</small>
          </span>
        </label>

        <div class="v9-residual-grid">
          <NumericField label="最大邻居数" :model-value="residual.maxNeighbors" :min="4" :max="256" :step="1" @update:model-value="setResidualNumber('maxNeighbors', $event)" />
          <NumericField label="最大基底数" :model-value="residual.maxBasis" :min="1" :max="32" :step="1" @update:model-value="setResidualNumber('maxBasis', $event)" />
          <NumericField label="最少邻居数" :model-value="residual.minNeighbors" :min="1" :max="64" :step="1" @update:model-value="setResidualNumber('minNeighbors', $event)" />
          <NumericField label="序位距离衰减" :model-value="residual.positionDecay" :min="0" :max="4" :step="0.01" @update:model-value="setResidualNumber('positionDecay', $event)" />
          <NumericField label="语义钟形峰值" :model-value="residual.semanticPeak" :min="-1" :max="1" :step="0.01" @update:model-value="setResidualNumber('semanticPeak', $event)" />
          <NumericField label="语义钟形宽度" :model-value="residual.semanticSigma" :min="0.02" :max="2" :step="0.01" @update:model-value="setResidualNumber('semanticSigma', $event)" />
          <NumericField label="语义软底" :model-value="residual.semanticFloor" :min="0" :max="1" :step="0.01" @update:model-value="setResidualNumber('semanticFloor', $event)" />
          <NumericField label="语义硬门槛" :model-value="residual.semanticHardFloor" :min="-1" :max="1" :step="0.01" @update:model-value="setResidualNumber('semanticHardFloor', $event)" />
          <NumericField label="最小解释增益" :model-value="residual.minGain" :min="0" :max="1" :step="0.001" @update:model-value="setResidualNumber('minGain', $event)" />
        </div>

        <div class="v9-anchor">
          <header>
            <div>
              <strong>V9 固定锚增益映射</strong>
              <small>原始 residual ratio 保持 [0,1]，工程增益使用固定函数映射。</small>
            </div>
            <code>clip(base + scale × r^γ)</code>
          </header>
          <div class="v9-residual-grid">
            <NumericField label="基础增益" :model-value="residual.v9AnchorBase" :min="0" :max="4" :step="0.01" @update:model-value="setResidualNumber('v9AnchorBase', $event)" />
            <NumericField label="残差倍率" :model-value="residual.v9AnchorScale" :min="0" :max="4" :step="0.01" @update:model-value="setResidualNumber('v9AnchorScale', $event)" />
            <NumericField label="曲线指数 γ" :model-value="residual.v9AnchorGamma" :min="0.1" :max="8" :step="0.01" @update:model-value="setResidualNumber('v9AnchorGamma', $event)" />
            <NumericField label="增益下限" :model-value="residual.v9AnchorMin" :min="0" :max="4" :step="0.01" @update:model-value="setResidualNumber('v9AnchorMin', $event)" />
            <NumericField label="增益上限" :model-value="residual.v9AnchorMax" :min="0" :max="8" :step="0.01" @update:model-value="setResidualNumber('v9AnchorMax', $event)" />
          </div>
        </div>
      </article>
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed } from "vue";
import type { ParamGroup, ParamValue } from "@/api";
import UiBadge from "@/components/ui/UiBadge.vue";
import UiSelect from "@/components/ui/UiSelect.vue";
import NumericField from "./TagMemoV9NumericField.vue";

type LooseRecord = Record<string, ParamValue>;

const props = defineProps<{
  modelValue: ParamGroup;
}>();

const emit = defineEmits<{
  "update:modelValue": [value: ParamGroup];
}>();

const versioning = computed(() => asRecord(props.modelValue.tagMemoVersioning));
const v9 = computed(() => ({
  outboundMass: numberValue(asRecord(props.modelValue.v9).outboundMass, 0.95),
  evidenceCompression: numberValue(asRecord(props.modelValue.v9).evidenceCompression, 1),
  wormholeGain: numberValue(asRecord(props.modelValue.v9).wormholeGain, 1.35),
  tensionThreshold: numberValue(asRecord(props.modelValue.v9).tensionThreshold, 1),
}));
const residual = computed(() => {
  const raw = asRecord(props.modelValue.intrinsicResidual);
  return {
    method: stringValue(raw.method, "anchored_gs"),
    maxNeighbors: numberValue(raw.maxNeighbors, 48),
    maxBasis: numberValue(raw.maxBasis, 4),
    minNeighbors: numberValue(raw.minNeighbors, 3),
    semanticEnabled: booleanValue(raw.semanticEnabled, true),
    semanticPeak: numberValue(raw.semanticPeak, 0.65),
    semanticSigma: numberValue(raw.semanticSigma, 0.25),
    semanticFloor: numberValue(raw.semanticFloor, 0.35),
    semanticHardFloor: numberValue(raw.semanticHardFloor, -1),
    minGain: numberValue(raw.minGain, 0.015),
    positionDecay: numberValue(raw.positionDecay, 0.15),
    v9AnchorBase: numberValue(raw.v9AnchorBase, 0.75),
    v9AnchorScale: numberValue(raw.v9AnchorScale, 1.25),
    v9AnchorGamma: numberValue(raw.v9AnchorGamma, 1),
    v9AnchorMin: numberValue(raw.v9AnchorMin, 0.5),
    v9AnchorMax: numberValue(raw.v9AnchorMax, 2),
  };
});

const activeVersion = computed(() => stringValue(versioning.value.activeVersion, "v8_3"));
const fallbackVersion = computed(() => stringValue(versioning.value.fallbackVersion, "v8_3"));
const dualArtifacts = computed(() => booleanValue(versioning.value.dualArtifacts, true));
const strictExplicitVersion = computed(() => booleanValue(versioning.value.strictExplicitVersion, true));
const activeVersionLabel = computed(() => activeVersion.value === "v9" ? "V9" : "V8.3");
const fallbackVersionLabel = computed(() => fallbackVersion.value === "v9" ? "V9" : "V8.3");

function asRecord(value: ParamValue | undefined): LooseRecord {
  return value && typeof value === "object" && !Array.isArray(value)
    ? value as LooseRecord
    : {};
}

function numberValue(value: ParamValue | undefined, fallback: number): number {
  return typeof value === "number" && Number.isFinite(value) ? value : fallback;
}

function stringValue(value: ParamValue | undefined, fallback: string): string {
  return typeof value === "string" ? value : fallback;
}

function booleanValue(value: ParamValue | undefined, fallback: boolean): boolean {
  if (typeof value === "boolean") return value;
  if (typeof value === "number") return value !== 0;
  return fallback;
}

function updateSection(section: string, key: string, value: ParamValue): void {
  emit("update:modelValue", {
    ...props.modelValue,
    [section]: {
      ...asRecord(props.modelValue[section]),
      [key]: value,
    },
  });
}

function setVersionField(key: string, value: string | number): void {
  updateSection("tagMemoVersioning", key, String(value));
}

function setVersionBoolean(key: string, event: Event): void {
  updateSection("tagMemoVersioning", key, (event.target as HTMLInputElement).checked);
}

function setV9Number(key: string, value: number): void {
  updateSection("v9", key, value);
}

function setResidualField(key: string, value: string | number): void {
  updateSection("intrinsicResidual", key, String(value));
}

function setResidualNumber(key: string, value: number): void {
  updateSection("intrinsicResidual", key, value);
}

function setResidualBoolean(key: string, event: Event): void {
  updateSection("intrinsicResidual", key, (event.target as HTMLInputElement).checked);
}

function clamp(value: number, min: number, max: number): number {
  return Math.min(max, Math.max(min, value));
}

function formatNumber(value: number): string {
  return Number.isInteger(value) ? String(value) : value.toFixed(3).replace(/0+$/, "").replace(/\.$/, "");
}
</script>

<style scoped>
.v9-console {
  display: grid;
  gap: var(--space-4);
  padding: var(--space-5);
  border: 1px solid color-mix(in srgb, var(--highlight-text) 30%, var(--border-color));
  border-radius: var(--radius-xl);
  background:
    radial-gradient(circle at 8% 0%, color-mix(in srgb, var(--highlight-text) 10%, transparent), transparent 34%),
    color-mix(in srgb, var(--primary-text) 1.5%, transparent);
}

.v9-console__header,
.v9-card__header,
.v9-anchor header {
  display: flex;
  justify-content: space-between;
  gap: var(--space-4);
  align-items: flex-start;
}

.v9-console__identity {
  display: flex;
  gap: var(--space-3);
}

.v9-console__identity > .material-symbols-outlined {
  display: grid;
  place-items: center;
  flex: 0 0 44px;
  height: 44px;
  border-radius: var(--radius-full);
  background: color-mix(in srgb, var(--highlight-text) 14%, transparent);
  color: var(--highlight-text);
  font-size: 26px;
}

.v9-console h3,
.v9-console h4,
.v9-console p {
  margin: 0;
}

.v9-console h3 {
  margin-top: 4px;
  font-size: var(--font-size-section-title-strong);
}

.v9-console__identity p {
  max-width: 76ch;
  margin-top: 8px;
  color: var(--secondary-text);
  line-height: 1.6;
}

.v9-console__eyebrow,
.v9-card__kicker {
  color: var(--highlight-text);
  font-size: var(--font-size-caption);
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.v9-console__status {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-end;
  gap: var(--space-2);
}

.v9-console__warning {
  display: flex;
  gap: var(--space-3);
  align-items: center;
  padding: var(--space-3);
  border: 1px solid color-mix(in srgb, var(--warning-border) 62%, var(--border-color));
  border-radius: var(--radius-md);
  background: color-mix(in srgb, var(--warning-bg) 56%, transparent);
}

.v9-console__warning p {
  color: var(--secondary-text);
  line-height: 1.55;
}

.v9-console__warning .material-symbols-outlined {
  color: var(--warning-color);
}

.v9-console__grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: var(--space-4);
}

.v9-card {
  display: grid;
  align-content: start;
  gap: var(--space-4);
  padding: var(--space-4);
  border: 1px solid color-mix(in srgb, var(--border-color) 78%, transparent);
  border-radius: var(--radius-lg);
  background: color-mix(in srgb, var(--primary-bg) 44%, transparent);
}

.v9-card--residual {
  grid-column: 1 / -1;
}

.v9-card__header h4 {
  margin-top: 4px;
  font-size: var(--font-size-title);
}

.v9-field {
  display: grid;
  gap: 6px;
}

.v9-field__label {
  font-weight: 600;
}

.v9-field small,
.v9-toggle small,
.v9-budget small,
.v9-anchor small {
  color: var(--secondary-text);
  line-height: 1.45;
}

.v9-toggle-list {
  display: grid;
  gap: var(--space-2);
}

.v9-toggle {
  display: flex;
  gap: var(--space-3);
  align-items: flex-start;
  padding: var(--space-3);
  border: 1px solid color-mix(in srgb, var(--border-color) 68%, transparent);
  border-radius: var(--radius-md);
}

.v9-toggle input {
  margin-top: 3px;
  accent-color: var(--highlight-text);
}

.v9-toggle span {
  display: grid;
  gap: 4px;
}

.v9-toggle--standalone {
  background: color-mix(in srgb, var(--highlight-text) 3%, transparent);
}

.v9-budget {
  display: grid;
  gap: var(--space-2);
  padding: var(--space-3);
  border-radius: var(--radius-md);
  background: color-mix(in srgb, var(--highlight-text) 5%, transparent);
}

.v9-budget > div:first-child {
  display: flex;
  justify-content: space-between;
}

.v9-budget__track {
  height: 8px;
  overflow: hidden;
  border-radius: var(--radius-full);
  background: color-mix(in srgb, var(--secondary-text) 20%, transparent);
}

.v9-budget__track span {
  display: block;
  height: 100%;
  border-radius: inherit;
  background: linear-gradient(90deg, var(--highlight-text), var(--warning-color));
}

.v9-residual-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: var(--space-3);
}

.v9-anchor {
  display: grid;
  gap: var(--space-3);
  padding-top: var(--space-4);
  border-top: 1px solid var(--border-color);
}

.v9-anchor header > div {
  display: grid;
  gap: 4px;
}

.v9-anchor code {
  padding: 6px 10px;
  border-radius: var(--radius-sm);
  background: color-mix(in srgb, var(--primary-text) 6%, transparent);
  color: var(--highlight-text);
}

@media (max-width: 1100px) {
  .v9-console__grid,
  .v9-residual-grid {
    grid-template-columns: 1fr 1fr;
  }

  .v9-card--residual {
    grid-column: auto;
  }
}

@media (max-width: 760px) {
  .v9-console {
    padding: var(--space-4);
  }

  .v9-console__header,
  .v9-card__header,
  .v9-anchor header {
    flex-direction: column;
  }

  .v9-console__status {
    justify-content: flex-start;
  }

  .v9-console__grid,
  .v9-residual-grid {
    grid-template-columns: 1fr;
  }
}
</style>