<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue';

type SectionKey = 'hero' | 'flow' | 'tender' | 'bid' | 'engine' | 'contact';

interface NavItem {
  key: SectionKey;
  label: string;
}

interface FeatureCard {
  label: string;
  title: string;
  description: string;
}

const sections: NavItem[] = [
  { key: 'hero', label: 'Intro' },
  { key: 'flow', label: 'Platform' },
  { key: 'tender', label: 'Tender' },
  { key: 'bid', label: 'Bid' },
  { key: 'engine', label: 'AI Engine' },
  { key: 'contact', label: 'Contact' }
];

const tenderFeatures: FeatureCard[] = [
  {
    label: 'Tender Planning',
    title: '招标文件策划',
    description: '从市场、项目、招标方维度生成项目需求可行性分析。'
  },
  {
    label: 'Document Drafting',
    title: '招标文件编制',
    description: '结合项目需求、政策法规与范本，辅助生成招标文件初稿。'
  },
  {
    label: 'Evaluation Review',
    title: '智能评审辅助',
    description: '围绕符合性、评分项与串标风险，形成可追溯审查链路。'
  }
];

const bidFeatures: FeatureCard[] = [
  {
    label: 'Requirement Parsing',
    title: '慧眼识标',
    description: '解析招标文件，抽取资格、商务、技术与交付要求。'
  },
  {
    label: 'Proposal Writing',
    title: '标书智写',
    description: '结合企业知识库和历史素材，生成可编辑的标书内容。'
  },
  {
    label: 'Risk Inspection',
    title: '火眼金睛 / 洞若观标',
    description: '查重、报价、图片、风险项与交付前审查一体完成。'
  }
];

const engineItems = ['文档解析', '规则抽取', '知识复用', '内容生成', '风险识别', '评分辅助'];
const activeSection = ref<SectionKey>('hero');
const showContact = ref(false);
const sectionRefs = new Map<SectionKey, HTMLElement>();
let sectionObserver: IntersectionObserver | null = null;

const tenderLoginUrl = import.meta.env.VITE_TENDER_LOGIN_URL || '';
const bidLoginUrl = import.meta.env.VITE_BID_LOGIN_URL || '';
const contactUrl = import.meta.env.VITE_CONTACT_URL || '';
const demoUrl = import.meta.env.VITE_DEMO_URL || '';

const activeIndex = computed(() => sections.findIndex(item => item.key === activeSection.value));

function setSectionRef(key: SectionKey, element: Element | null) {
  if (element instanceof HTMLElement) {
    sectionRefs.set(key, element);
  }
}

function scrollToSection(key: SectionKey) {
  sectionRefs.get(key)?.scrollIntoView({ behavior: 'smooth', block: 'start' });
}

function openUrlOrContact(url: string) {
  if (url) {
    window.open(url, '_blank', 'noopener,noreferrer');
    return;
  }

  showContact.value = true;
}

function closeContact() {
  showContact.value = false;
}

onMounted(() => {
  sectionObserver = new IntersectionObserver(
    entries => {
      const visible = entries
        .filter(entry => entry.isIntersecting)
        .sort((a, b) => b.intersectionRatio - a.intersectionRatio)[0];

      const key = visible?.target.getAttribute('data-section') as SectionKey | null;
      if (key) {
        activeSection.value = key;
      }
    },
    { threshold: [0.35, 0.55, 0.75] }
  );

  sectionRefs.forEach(element => sectionObserver?.observe(element));
});

onUnmounted(() => {
  sectionObserver?.disconnect();
  sectionObserver = null;
});
</script>

<template>
  <main class="landing-shell">
    <header class="site-nav">
      <button class="brand-mark" type="button" @click="scrollToSection('hero')">
        <span class="brand-symbol">B</span>
        <span>
          <strong>BidFlow AI</strong>
          <small>智标宝</small>
        </span>
      </button>

      <nav class="nav-links" aria-label="Main navigation">
        <button
          v-for="item in sections.slice(1)"
          :key="item.key"
          type="button"
          :class="{ active: activeSection === item.key }"
          @click="scrollToSection(item.key)"
        >
          {{ item.label }}
        </button>
      </nav>

      <div class="nav-actions">
        <button type="button" class="ghost-btn" @click="openUrlOrContact(tenderLoginUrl)">
          招采平台
        </button>
        <button type="button" class="solid-btn" @click="openUrlOrContact(bidLoginUrl)">
          投标平台
        </button>
      </div>
    </header>

    <aside class="section-indicator" aria-label="Section progress">
      <button
        v-for="(item, index) in sections"
        :key="item.key"
        type="button"
        :class="{ active: activeSection === item.key }"
        @click="scrollToSection(item.key)"
      >
        <span>{{ String(index + 1).padStart(2, '0') }}</span>
        <i />
        <strong>{{ item.label }}</strong>
      </button>
    </aside>

    <section
      :ref="element => setSectionRef('hero', element)"
      class="snap-section hero-section"
      data-section="hero"
    >
      <div class="hero-copy">
        <p class="eyebrow">Tender & Bid AI Operating System</p>
        <h1>招采与投标，一站式 AI 全流程平台</h1>
        <p class="hero-subtitle">From Tender Planning to Bid Delivery.</p>
        <p class="hero-description">
          智标宝 BidFlow AI 覆盖招标策划、文件编制、智能评审、识标决策、标书生成与风险校验，让招投标从经验驱动走向 AI 协同。
        </p>
        <div class="hero-actions">
          <button type="button" class="solid-btn large" @click="openUrlOrContact(tenderLoginUrl)">
            进入招采平台
          </button>
          <button type="button" class="ghost-btn large" @click="openUrlOrContact(bidLoginUrl)">
            进入投标平台
          </button>
          <button type="button" class="text-btn" @click="openUrlOrContact(demoUrl)">
            预约演示
          </button>
        </div>
      </div>

      <div class="hero-visual" aria-label="AI tender and bid workbench visual">
        <video
          class="media-slot"
          src="/media/hero-ai-workbench.mp4"
          poster="/media/hero-ai-workbench-poster.webp"
          muted
          loop
          playsinline
          autoplay
        />
        <div class="visual-fallback">
          <div class="document-card document-card-main">
            <span>BidFlow AI</span>
            <strong>全流程作业台</strong>
            <p>Tender · Bid · Review</p>
          </div>
          <div class="floating-tag tag-purple">AI Engine</div>
          <div class="floating-tag tag-blue">规则抽取</div>
          <div class="floating-tag tag-orange">风险校验</div>
          <div class="flow-line flow-line-one" />
          <div class="flow-line flow-line-two" />
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('flow', element)"
      class="snap-section flow-section"
      data-section="flow"
    >
      <div class="section-copy">
        <p class="eyebrow">One Platform. Two Workflows.</p>
        <h2>一套平台，连接招标方与投标方的完整作业链</h2>
      </div>

      <div class="flow-map">
        <div class="flow-column">
          <span class="column-label tender-color">Tender Suite</span>
          <strong>招采端</strong>
          <p>策划、编制、评审、审查、评分</p>
        </div>
        <div class="engine-core">
          <span>AI</span>
          <strong>BidFlow Core</strong>
          <p>文档、规则、知识与交付中枢</p>
        </div>
        <div class="flow-column">
          <span class="column-label bid-color">Bid Suite</span>
          <strong>投标端</strong>
          <p>识标、写标、查标、审标、交付</p>
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('tender', element)"
      class="snap-section suite-section tender-section"
      data-section="tender"
    >
      <div class="section-copy">
        <p class="eyebrow">Tender Suite</p>
        <h2>面向招采方的 AI 招采工作台</h2>
        <p>支持从需求策划、招标文件编制到评标审查的关键环节，帮助招采团队更快形成可追溯、可复核的工作结果。</p>
      </div>

      <div class="feature-grid">
        <article v-for="feature in tenderFeatures" :key="feature.title" class="feature-card">
          <span>{{ feature.label }}</span>
          <h3>{{ feature.title }}</h3>
          <p>{{ feature.description }}</p>
        </article>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('bid', element)"
      class="snap-section suite-section bid-section"
      data-section="bid"
    >
      <div class="section-copy">
        <p class="eyebrow">Bid Suite</p>
        <h2>面向投标方的 AI 投标作业系统</h2>
        <p>从读懂招标文件到生成标书、检查风险、交付前复核，把企业投标资产沉淀为可复用的作业能力。</p>
      </div>

      <div class="feature-grid">
        <article v-for="feature in bidFeatures" :key="feature.title" class="feature-card dark-card">
          <span>{{ feature.label }}</span>
          <h3>{{ feature.title }}</h3>
          <p>{{ feature.description }}</p>
        </article>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('engine', element)"
      class="snap-section engine-section"
      data-section="engine"
    >
      <div class="section-copy">
        <p class="eyebrow">AI Engine</p>
        <h2>把文档、规则、知识和交付连接起来</h2>
        <p>BidFlow AI 不只是生成文字，而是围绕招投标作业链提供解析、抽取、生成、检查与辅助决策能力。</p>
      </div>

      <div class="engine-orbit">
        <div class="orbit-core">BidFlow<br />Core</div>
        <span v-for="item in engineItems" :key="item">{{ item }}</span>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('contact', element)"
      class="snap-section contact-section"
      data-section="contact"
    >
      <div class="contact-panel">
        <p class="eyebrow">Start With A Demo</p>
        <h2>从一次演示开始，重塑招投标作业方式</h2>
        <p>选择进入平台，或联系我们安排产品演示。正式联系方式和二维码可后续放入素材位。</p>
        <div class="hero-actions">
          <button type="button" class="solid-btn large" @click="openUrlOrContact(tenderLoginUrl)">
            进入招采平台
          </button>
          <button type="button" class="ghost-btn large" @click="openUrlOrContact(bidLoginUrl)">
            进入投标平台
          </button>
          <button type="button" class="text-btn" @click="openUrlOrContact(contactUrl)">
            联系我们
          </button>
        </div>
      </div>
    </section>

    <div v-if="showContact" class="contact-modal" role="dialog" aria-modal="true">
      <div class="modal-card">
        <button class="modal-close" type="button" aria-label="Close" @click="closeContact">×</button>
        <p class="eyebrow">Contact</p>
        <h2>联系方式待配置</h2>
        <p>这里预留企业微信、电话、邮箱或预约演示表单。配置环境变量后按钮会直接跳转。</p>
        <div class="qr-placeholder">QR</div>
      </div>
    </div>
  </main>
</template>
