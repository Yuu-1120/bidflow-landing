<script setup lang="ts">
withDefaults(
  defineProps<{
    label?: string;
    tone?: 'primary' | 'secondary';
    size?: 'nav' | 'hero';
    type?: 'button' | 'submit' | 'reset';
  }>(),
  {
    label: '',
    tone: 'primary',
    size: 'nav',
    type: 'button'
  }
);

const emit = defineEmits<{
  click: [event: MouseEvent];
}>();
</script>

<template>
  <button
    class="liquid-glass-button"
    :class="[`liquid-glass-button--${tone}`, `liquid-glass-button--${size}`]"
    :type="type"
    @click="emit('click', $event)"
  >
    <span class="liquid-glass-button__surface" aria-hidden="true" />
    <span class="liquid-glass-button__content">
      <slot>{{ label }}</slot>
    </span>
  </button>
</template>

<style scoped>
.liquid-glass-button {
  position: relative;
  isolation: isolate;
  display: inline-grid;
  place-items: center;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.72);
  border-radius: 999px;
  color: #20202a;
  background:
    linear-gradient(180deg, rgba(255, 255, 255, 0.58), rgba(241, 239, 249, 0.3)),
    rgba(255, 255, 255, 0.2);
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.82),
    inset 0 -1px 0 rgba(102, 99, 125, 0.08),
    0 14px 34px rgba(78, 73, 106, 0.09);
  cursor: pointer;
  transition:
    border-color 180ms ease,
    box-shadow 180ms ease,
    background 180ms ease;
  backdrop-filter: blur(22px) saturate(1.28);
  -webkit-backdrop-filter: blur(22px) saturate(1.28);
}

.liquid-glass-button--nav {
  min-width: 112px;
  min-height: 42px;
  padding: 0 18px;
  border-radius: 999px;
}

.liquid-glass-button--hero {
  min-width: 154px;
  min-height: 56px;
  padding: 0 22px;
  border-radius: 18px;
  font-weight: 700;
}

.liquid-glass-button--primary {
  border-color: rgba(255, 255, 255, 0.12);
  color: white;
  background:
    linear-gradient(180deg, rgba(45, 44, 53, 0.98), rgba(15, 15, 21, 0.98)),
    #181820;
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.18),
    inset 0 -10px 20px rgba(0, 0, 0, 0.2),
    0 14px 30px rgba(31, 29, 42, 0.18);
}

.liquid-glass-button--secondary {
  border-color: rgba(146, 139, 165, 0.2);
  color: rgba(39, 37, 49, 0.72);
  background:
    linear-gradient(180deg, rgba(255, 255, 255, 0.42), rgba(241, 239, 248, 0.22)),
    rgba(245, 243, 250, 0.2);
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.68),
    inset 0 -1px 0 rgba(102, 99, 125, 0.06),
    0 12px 28px rgba(76, 73, 104, 0.07);
}

.liquid-glass-button__surface {
  position: absolute;
  pointer-events: none;
  border-radius: inherit;
}

.liquid-glass-button__surface {
  inset: 1px;
  z-index: -1;
  background:
    linear-gradient(180deg, rgba(255, 255, 255, 0.24), transparent 38%, rgba(255, 255, 255, 0.04)),
    radial-gradient(ellipse at 50% 0%, rgba(255, 255, 255, 0.26), transparent 48%),
    radial-gradient(ellipse at 88% 48%, rgba(132, 105, 255, 0.18), transparent 36%);
  mix-blend-mode: screen;
  opacity: 0.5;
  transition:
    opacity 180ms ease,
    filter 180ms ease;
}

.liquid-glass-button__content {
  position: relative;
  z-index: 1;
  display: inline-flex;
  gap: 12px;
  align-items: center;
  justify-content: center;
  white-space: nowrap;
}

.liquid-glass-button--hero.liquid-glass-button--primary .liquid-glass-button__content::after {
  display: inline-flex;
  height: 18px;
  align-items: center;
  padding-left: 11px;
  color: rgba(255, 255, 255, 0.78);
  font-size: 15px;
  font-weight: 520;
  line-height: 1;
  content: "→";
}

.liquid-glass-button--hero.liquid-glass-button--primary .liquid-glass-button__content {
  gap: 0;
}

.liquid-glass-button:hover {
  border-color: rgba(255, 255, 255, 0.76);
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.92),
    inset 0 -1px 0 rgba(102, 99, 125, 0.1),
    0 14px 30px rgba(78, 73, 106, 0.1);
}

.liquid-glass-button--primary:hover {
  border-color: rgba(255, 255, 255, 0.28);
  box-shadow:
    inset 0 1px 0 rgba(255, 255, 255, 0.28),
    inset 0 -12px 24px rgba(0, 0, 0, 0.24),
    0 14px 30px rgba(31, 29, 42, 0.2);
}

.liquid-glass-button:hover .liquid-glass-button__surface {
  opacity: 0.68;
  filter: saturate(1.08);
}

.liquid-glass-button:active {
  box-shadow:
    inset 0 2px 8px rgba(0, 0, 0, 0.28),
    inset 0 -1px 0 rgba(255, 255, 255, 0.08),
    0 8px 18px rgba(76, 73, 104, 0.08);
}

.liquid-glass-button:focus-visible {
  outline: 3px solid rgba(123, 77, 255, 0.24);
  outline-offset: 4px;
}

@media (prefers-reduced-motion: reduce) {
  .liquid-glass-button,
  .liquid-glass-button__surface {
    transition: none;
  }
}
</style>
