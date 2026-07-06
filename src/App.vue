<script setup lang="ts">
import { computed, nextTick, onMounted, onUnmounted, ref } from 'vue';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { ScrambleTextPlugin } from 'gsap/ScrambleTextPlugin';
import LiquidGlassButton from './components/LiquidGlassButton.vue';

gsap.registerPlugin(ScrollTrigger, ScrambleTextPlugin);

type SectionKey = 'hero' | 'pain' | 'solution' | 'bid' | 'tender' | 'value' | 'security' | 'trust' | 'contact';

type ModalKind = 'demo' | 'contact' | 'feedback';

interface NavItem {
  key: SectionKey;
  label: string;
}

interface ContentCard {
  label: string;
  title: string;
  description: string;
}

interface MetricCard {
  value: string;
  label: string;
  description: string;
}

interface ProductRow {
  title: string;
  meta: string;
  state: string;
}

interface CompanyStat {
  value: string;
  label: string;
}

interface ContactAction {
  label: string;
  title: string;
  description: string;
  kind: ModalKind;
}

interface ModalCopy {
  eyebrow: string;
  title: string;
  description: string;
  submitLabel: string;
  successTitle: string;
  successText: string;
}

const sections: NavItem[] = [
  { key: 'hero', label: '首页' },
  { key: 'pain', label: '客户痛点' },
  { key: 'solution', label: '产品价值' },
  { key: 'bid', label: '投标平台' },
  { key: 'tender', label: '招采平台' },
  { key: 'value', label: '效果数据' },
  { key: 'security', label: '安全部署' },
  { key: 'trust', label: '公司背书' },
  { key: 'contact', label: '联系我们' }
];

const navItems = sections.filter(item =>
  ['solution', 'bid', 'tender', 'security', 'trust', 'contact'].includes(item.key)
);

const navActiveIndex = computed(() => navItems.findIndex(item => item.key === activeSection.value));
const navIndicatorStyle = computed<Record<string, string>>(() => ({
  '--nav-active-index': String(Math.max(navActiveIndex.value, 0)),
  '--nav-active-visible': navActiveIndex.value >= 0 ? '1' : '0'
}));

const painPoints: ContentCard[] = [
  {
    label: 'Scattered Assets',
    title: '资料分散，反复整理',
    description: '企业资质、产品资料、历史标书和业绩案例散落在不同文件夹，每次投标都要重新找、重新填。'
  },
  {
    label: 'Heavy Drafting',
    title: '写标耗时，改稿反复',
    description: '条款多、章节多、响应要求细，技术标和资信标都需要大量人工检索、拼装和校对。'
  },
  {
    label: 'Compliance Risk',
    title: '风险隐蔽，废标代价高',
    description: '承诺漏项、资质过期、格式错配、弱响应项和雷同内容，都可能在最后阶段才暴露。'
  },
  {
    label: 'Manual Review',
    title: '评审繁重，依据难追溯',
    description: '串标排查、符合性评审、评分规则确认依赖人工，效率、一致性和留痕都难保障。'
  }
];

const solutionNodes: ContentCard[] = [
  {
    label: 'Tender Suite',
    title: '招采平台',
    description: '覆盖需求策划、招标文件编制、评标项目、串标审查、符合性评审和智能评分。'
  },
  {
    label: 'AI Engine',
    title: 'BidFlow AI Core',
    description: '连接文档解析、规则抽取、知识复用、内容生成、风险识别与评分辅助。'
  },
  {
    label: 'Bid Suite',
    title: '投标平台',
    description: '围绕识标、写标、查重、审标、企资库、产品库和知识库形成投标作业闭环。'
  }
];

const bidCapabilities: ContentCard[] = [
  {
    label: '慧眼识标',
    title: '精准解析招标文件',
    description: '抽取资格、商务、技术、评分与承诺要求，让投标团队快速抓住核心响应点。'
  },
  {
    label: '标书智写',
    title: '调用知识资产生成标书',
    description: '结合企业知识库、历史标书和优质素材，辅助生成可编辑、可复核的标书内容。'
  },
  {
    label: '火眼金睛',
    title: '语义查重与雷同风险识别',
    description: '支持多标书比对，定位重复段落、相似内容和潜在雷同风险，提升交付前把控。'
  },
  {
    label: '洞若观标',
    title: '逐条款审标与批注',
    description: '比对招投标文件差异，自动批注风险、漏项与弱响应问题，支持人工修订留痕。'
  },
  {
    label: '企资 / 产品 / 知识库',
    title: '把历史经验变成可复用资产',
    description: '企业资料、产品参数、案例方案结构化沉淀，下次写标时一键调用，不再重复录入。'
  }
];

const tenderCapabilities: ContentCard[] = [
  {
    label: '招标文件策划',
    title: '从项目需求到招标建议',
    description: '围绕市场情况、同类项目和资源匹配度，辅助形成需求可行性分析与招标建议。'
  },
  {
    label: '招标文件编制',
    title: '范本撰写与定向撰写',
    description: '支持招标公告、投标人须知、评标办法、技术规格、合同条款等内容生成。'
  },
  {
    label: '评标项目确认',
    title: '自动提取评审规则',
    description: '从招标文件中提取废标项、符合性评审和详细评分规则，减少人工整理成本。'
  },
  {
    label: '串标审查',
    title: '多维识别相似风险',
    description: '按商务资格、技术文件、报价文件和元数据等维度识别投标文件间的异常相似。'
  },
  {
    label: '符合性评审 / 智能评分',
    title: '形成可追溯评审依据',
    description: '输出审查结果、评分说明、排名与报告，让评审过程更高效、更可复核。'
  }
];

const bidWorkbenchSteps = ['识标解析', '知识匹配', '内容生成', '语义查重', '审标批注'];
const bidAssetSources = ['招标文件.pdf', '企业资质库', '历史优质标书', '产品参数库'];
const tenderPipeline = ['需求策划', '文件编制', '评标规则', '审查评分', '报告留痕'];
const tenderReviewRows: ProductRow[] = [
  { title: '符合性评审', meta: '12 项规则已抽取', state: 'Ready' },
  { title: '串标审查', meta: '多文件相似度比对', state: 'Scan' },
  { title: '智能评分', meta: '评分依据自动归档', state: 'Live' }
];

const metrics: MetricCard[] = [
  {
    value: '30%-81%',
    label: '标书撰写提效',
    description: '来自产品手册中的典型场景表达，实际效果以项目落地为准。'
  },
  {
    value: '30%',
    label: '废标率降低参考',
    description: '通过风险预警、合规检查和交付前复核，提前暴露关键问题。'
  },
  {
    value: '80%',
    label: '知识利用率提升',
    description: '历史标书、资质文件、业绩案例沉淀为可检索、可调用的企业资产。'
  },
  {
    value: '3 min',
    label: '解析核心要素',
    description: '在大型集团案例中，用于快速识别废标项、应答项等核心信息。'
  }
];

const securityItems: ContentCard[] = [
  {
    label: 'Private Deployment',
    title: '私有化安全部署',
    description: '支持本地私有化部署与软硬一体机形态，面向集团推广和敏感数据场景。'
  },
  {
    label: 'Permission Control',
    title: '权限分级与全流程留痕',
    description: '按岗位、项目、角色划分操作权限，协作、审查和人工调整过程都可追溯。'
  },
  {
    label: 'Domestic Computing',
    title: '国产算力与生态适配',
    description: '可结合国产算力硬件与行业软件一体化集成，降低部署运维复杂度。'
  }
];

const trustCards: ContentCard[] = [
  {
    label: 'Company',
    title: '南京炫佳网络科技有限公司',
    description: '2013 年成立于南京江北新区，长期深耕数字内容与 AI 工业化生产场景。'
  },
  {
    label: 'Capital',
    title: 'B 轮累计融资超 2 亿元',
    description: '多轮融资与产业资源持续加持，支撑产品能力升级和场景落地。'
  },
  {
    label: 'Awards',
    title: '生成式 AI 与算力创新奖项',
    description: '智标宝相关能力获得江苏生成式人工智能创新大赛、华彩杯等赛事认可。'
  }
];

const companyStats: CompanyStat[] = [
  { value: '2013', label: '公司成立' },
  { value: '40+', label: '专利' },
  { value: '150+', label: '社会荣誉' },
  { value: '300+', label: '软著' }
];

const contactActions: ContactAction[] = [
  {
    label: 'Demo',
    title: '预约演示',
    description: '按招采、投标、评审和安全部署场景，安排一次针对性产品演示。',
    kind: 'demo'
  },
  {
    label: 'Consult',
    title: '联系咨询',
    description: '了解平台入口、私有化部署、软硬一体机和企业采购配套。',
    kind: 'contact'
  },
  {
    label: 'Feedback',
    title: '客户反馈',
    description: '提交使用建议、问题反馈或行业场景需求，帮助产品持续迭代。',
    kind: 'feedback'
  }
];

const modalCopies: Record<ModalKind, ModalCopy> = {
  demo: {
    eyebrow: 'Book A Demo',
    title: '预约智标宝产品演示',
    description: '留下基本信息，我们会结合你的招采、投标、评审或安全部署场景安排演示内容。',
    submitLabel: '提交预约',
    successTitle: '预约信息已收到',
    successText: '我们会根据你填写的场景安排演示沟通。'
  },
  contact: {
    eyebrow: 'Contact',
    title: '联系智标宝团队',
    description: '用于产品咨询、部署方式沟通、平台开通和企业采购前期对接。',
    submitLabel: '提交咨询',
    successTitle: '咨询信息已收到',
    successText: '我们会尽快与你确认需求和后续沟通方式。'
  },
  feedback: {
    eyebrow: 'Feedback',
    title: '客户反馈',
    description: '欢迎提交产品建议、问题反馈、演示需求或行业场景补充。',
    submitLabel: '提交反馈',
    successTitle: '反馈已收到',
    successText: '感谢你的建议，智标宝团队会持续跟进产品体验。'
  }
};

const engineItems = ['文档解析', '规则抽取', '知识复用', '内容生成', '风险识别', '评分辅助'];
const landingShell = ref<HTMLElement | null>(null);
const activeSection = ref<SectionKey>('hero');
const modalKind = ref<ModalKind>('demo');
const showModal = ref(false);
const submittedModal = ref<ModalKind | null>(null);
const demoForm = ref({
  name: '',
  company: '',
  contact: '',
  role: '',
  platform: '招采平台 + 投标平台',
  scene: ''
});
const feedbackForm = ref({
  type: '产品建议',
  contact: '',
  title: '',
  content: ''
});
const sectionRefs = new Map<SectionKey, HTMLElement>();
let sectionObserver: IntersectionObserver | null = null;
let painMotionMedia: ReturnType<typeof gsap.matchMedia> | null = null;
let storyMotionMedia: ReturnType<typeof gsap.matchMedia> | null = null;
let storyScrollFrame = 0;
let cleanupStoryCanvas: (() => void) | null = null;

const tenderLoginUrl = import.meta.env.VITE_TENDER_LOGIN_URL || 'http://221.226.15.10:48089/tenderWriting/home';
const bidLoginUrl = import.meta.env.VITE_BID_LOGIN_URL || 'http://36.155.142.138:38089/bidWriting/home';
const contactUrl = import.meta.env.VITE_CONTACT_URL || '';
const demoUrl = import.meta.env.VITE_DEMO_URL || '';
const feedbackUrl = import.meta.env.VITE_FEEDBACK_URL || '';
const currentModalCopy = computed(() => modalCopies[modalKind.value]);
const modalSubmitted = computed(() => submittedModal.value === modalKind.value);

function setSectionRef(key: SectionKey, element: unknown) {
  if (element instanceof HTMLElement) {
    sectionRefs.set(key, element);
  }
}

function isActive(key: SectionKey) {
  return activeSection.value === key;
}

function scrollToSection(key: SectionKey) {
  sectionRefs.get(key)?.scrollIntoView({ behavior: 'smooth', block: 'start' });
}

function openUrlOrModal(url: string, kind: ModalKind) {
  if (url && kind === 'contact') {
    window.open(url, '_blank', 'noopener,noreferrer');
    return;
  }

  modalKind.value = kind;
  submittedModal.value = null;
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
}

function submitModal() {
  submittedModal.value = modalKind.value;
}

function restoreTextOnCleanup(element: HTMLElement | null, callbacks: Array<() => void>) {
  if (!element) {
    return;
  }

  const originalText = element.textContent ?? '';
  callbacks.push(() => {
    element.textContent = originalText;
  });
}

function addCountTextTween(
  timeline: gsap.core.Timeline,
  element: HTMLElement | null,
  finalText: string,
  position: gsap.Position,
  duration = 0.32
) {
  if (!element) {
    return;
  }

  const progressState = { value: 0 };
  const rangeMatch = finalText.match(/^(\d+)%-(\d+)%$/);
  const numericMatch = finalText.match(/^(\d+)(.*)$/);

  const render = () => {
    if (rangeMatch) {
      const start = Math.round(Number(rangeMatch[1]) * progressState.value);
      const end = Math.round(Number(rangeMatch[2]) * progressState.value);
      element.textContent = `${start}%-${end}%`;
      return;
    }

    if (numericMatch) {
      const value = Math.round(Number(numericMatch[1]) * progressState.value);
      element.textContent = `${value}${numericMatch[2]}`;
      return;
    }

    element.textContent = finalText;
  };

  render();
  timeline.to(
    progressState,
    {
      value: 1,
      duration,
      ease: 'power2.out',
      onUpdate: render,
      onComplete: () => {
        element.textContent = finalText;
      }
    },
    position
  );
}

function setupStoryCanvas() {
  const shell = landingShell.value;

  if (!shell) {
    return;
  }

  const readScrollState = () => {
    const shellMaxScroll = shell.scrollHeight - shell.clientHeight;

    if (shellMaxScroll > 4) {
      return {
        maxScroll: Math.max(shellMaxScroll, 1),
        scrollTop: shell.scrollTop,
        viewportHeight: shell.clientHeight
      };
    }

    const scrollingElement = document.scrollingElement ?? document.documentElement;
    const scrollTop = window.scrollY || scrollingElement.scrollTop;

    return {
      maxScroll: Math.max(scrollingElement.scrollHeight - window.innerHeight, 1),
      scrollTop,
      viewportHeight: window.innerHeight
    };
  };

  const updateStoryCanvas = () => {
    storyScrollFrame = 0;

    const { maxScroll, scrollTop, viewportHeight } = readScrollState();
    const rawProgress = scrollTop / maxScroll;
    const storyProgress = Math.min(Math.max((scrollTop - viewportHeight * 0.34) / maxScroll, 0), 1);
    const surfaceX = -6 + storyProgress * 12;
    const surfaceY = 6 + storyProgress * 9;
    const sheenX = 10 - storyProgress * 18;
    const sheenY = -8 + storyProgress * 12;
    const paperY = -storyProgress * 340;
    const gridY = -storyProgress * 210;

    shell.style.setProperty('--story-progress', storyProgress.toFixed(3));
    shell.style.setProperty('--story-depth-opacity', (storyProgress * 0.48).toFixed(3));
    shell.style.setProperty('--story-paper-opacity', (storyProgress * 0.52).toFixed(3));
    shell.style.setProperty('--story-thread-opacity', (storyProgress * 0.42).toFixed(3));
    shell.style.setProperty(
      '--story-surface-transform',
      `translate3d(${surfaceX.toFixed(2)}vw, ${surfaceY.toFixed(2)}vh, 0) rotate(${(storyProgress * 8 - 2).toFixed(2)}deg) scale(${(1 + storyProgress * 0.045).toFixed(3)})`
    );
    shell.style.setProperty(
      '--story-sheen-transform',
      `translate3d(${sheenX.toFixed(2)}vw, ${sheenY.toFixed(2)}vh, 0) rotate(${(-4 - storyProgress * 3).toFixed(2)}deg)`
    );
    shell.style.setProperty(
      '--story-paper-transform',
      `translate3d(${(-storyProgress * 7).toFixed(2)}vw, ${paperY.toFixed(1)}px, 0) rotate(${(storyProgress * 2.5).toFixed(2)}deg)`
    );
    shell.style.setProperty(
      '--story-thread-transform',
      `translate3d(${(storyProgress * 5).toFixed(2)}vw, ${(-storyProgress * 260).toFixed(1)}px, 0)`
    );
    shell.style.setProperty('--story-grid-transform', `translate3d(0, ${gridY.toFixed(1)}px, 0)`);
    shell.style.setProperty('--hero-blend-opacity', String(Math.min(Math.max(rawProgress * 2.8, 0), 1).toFixed(3)));
  };

  const requestStoryUpdate = () => {
    if (storyScrollFrame) {
      return;
    }

    storyScrollFrame = requestAnimationFrame(updateStoryCanvas);
  };

  updateStoryCanvas();
  shell.addEventListener('scroll', requestStoryUpdate, { passive: true });
  window.addEventListener('scroll', requestStoryUpdate, { passive: true });
  window.addEventListener('resize', requestStoryUpdate);

  cleanupStoryCanvas = () => {
    shell.removeEventListener('scroll', requestStoryUpdate);
    window.removeEventListener('scroll', requestStoryUpdate);
    window.removeEventListener('resize', requestStoryUpdate);

    if (storyScrollFrame) {
      cancelAnimationFrame(storyScrollFrame);
      storyScrollFrame = 0;
    }
  };
}

function setupHeroIntro(restoreCallbacks: Array<() => void>) {
  const heroSection = sectionRefs.get('hero');

  if (!heroSection) {
    return;
  }

  const eyebrow = heroSection.querySelector<HTMLElement>('.gallery-copy .eyebrow');
  const titleLines = gsap.utils.toArray<HTMLElement>('.hero-title-line', heroSection);
  const productName = heroSection.querySelector<HTMLElement>('.hero-title-product');
  const kicker = heroSection.querySelector<HTMLElement>('.hero-title-kicker');
  const description = heroSection.querySelector<HTMLElement>('.hero-description');
  const proof = heroSection.querySelector<HTMLElement>('.hero-proof');
  const actionTargets = gsap.utils.toArray<HTMLElement>('.hero-actions > *', heroSection);
  const dataItems = gsap.utils.toArray<HTMLElement>('.hero-data-strip span', heroSection);
  const dataDots = gsap.utils.toArray<HTMLElement>('.hero-data-strip i', heroSection);
  const introCopy = [eyebrow, description, proof].filter((target): target is HTMLElement => Boolean(target));
  const bodyCopy = [description, proof].filter((target): target is HTMLElement => Boolean(target));

  restoreTextOnCleanup(productName, restoreCallbacks);
  restoreTextOnCleanup(kicker, restoreCallbacks);

  const finalProductName = productName?.textContent?.trim() ?? '';

  gsap.set(introCopy, {
    autoAlpha: 0,
    y: 16,
    filter: 'blur(8px)'
  });
  gsap.set(titleLines, {
    autoAlpha: 0,
    yPercent: 112,
    clipPath: 'inset(0% 0% 100% 0%)'
  });
  gsap.set(kicker, {
    autoAlpha: 0,
    yPercent: 86,
    clipPath: 'inset(0% 0% 100% 0%)'
  });
  gsap.set(actionTargets, {
    autoAlpha: 0,
    y: 18,
    scale: 0.98,
    filter: 'blur(8px)'
  });
  gsap.set([...dataItems, ...dataDots], {
    autoAlpha: 0,
    y: 8,
    scale: 0.96
  });

  const timeline = gsap.timeline({
    defaults: {
      ease: 'power3.out'
    },
    delay: 0.16
  });

  if (eyebrow) {
    timeline.to(eyebrow, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.46 }, 0);
  }

  timeline.to(
    titleLines,
    { autoAlpha: 1, yPercent: 0, clipPath: 'inset(0% 0% 0% 0%)', duration: 0.78, stagger: 0.12 },
    0.12
  );

  if (productName && finalProductName) {
    timeline.to(
      productName,
      {
        duration: 0.76,
        scrambleText: {
          text: finalProductName,
          chars: '01TENDERBIDFLOWAI',
          revealDelay: 0.1
        }
      },
      0.34
    );
  }

  timeline
    .to(
      kicker,
      {
        autoAlpha: 1,
        yPercent: 0,
        clipPath: 'inset(0% 0% 0% 0%)',
        duration: 0.72
      },
      0.38
    )
    .to(bodyCopy, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.54, stagger: 0.08 }, 0.72)
    .to(actionTargets, { autoAlpha: 1, y: 0, scale: 1, filter: 'blur(0px)', duration: 0.54, stagger: 0.08 }, 0.92)
    .to(dataItems, { autoAlpha: 1, y: 0, scale: 1, duration: 0.36, stagger: 0.055 }, 1.08)
    .to(dataDots, { autoAlpha: 1, y: 0, scale: 1, duration: 0.28, stagger: 0.055 }, 1.14);
}

function setupSolutionFlow(shell: HTMLElement) {
  const section = sectionRefs.get('solution');

  if (!section) {
    return;
  }

  const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', section);
  const nodes = gsap.utils.toArray<HTMLElement>('.solution-node', section);
  const centerNode = section.querySelector<HTMLElement>('.solution-node.node-1');
  const sideNodes = nodes.filter(node => node !== centerNode);
  const lines = gsap.utils.toArray<HTMLElement>('.stage-line', section);

  gsap.set(copyItems, { autoAlpha: 0, y: 22, filter: 'blur(8px)' });
  gsap.set(centerNode, { autoAlpha: 0, scale: 0.86, y: 26, filter: 'blur(12px)' });
  gsap.set(sideNodes, {
    autoAlpha: 0,
    x: (index: number) => (index === 0 ? 92 : -92),
    y: 22,
    scale: 0.92,
    filter: 'blur(12px)'
  });
  gsap.set(lines, { scaleX: 0, autoAlpha: 0 });

  const timeline = gsap.timeline({
    scrollTrigger: {
      id: 'solution-flow-assembly',
      trigger: section,
      scroller: shell,
      start: 'top 70%',
      end: 'center 42%',
      toggleActions: 'play none none reverse',
      invalidateOnRefresh: true,
      refreshPriority: 2
    }
  });

  timeline
    .to(copyItems, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.5, stagger: 0.055, ease: 'power3.out' }, 0)
    .to(centerNode, { autoAlpha: 1, scale: 1, y: 0, filter: 'blur(0px)', duration: 0.64, ease: 'expo.out' }, 0.12)
    .to(lines, { autoAlpha: 1, scaleX: 1, duration: 0.5, stagger: 0.08, ease: 'power2.inOut' }, 0.38)
    .to(
      sideNodes,
      { autoAlpha: 1, x: 0, y: 0, scale: 1, filter: 'blur(0px)', duration: 0.62, stagger: 0.08, ease: 'expo.out' },
      0.48
    )
    .to(nodes, { '--node-glow': 1, duration: 0.32, stagger: 0.06, ease: 'power1.out' }, 0.82);
}

function setupBidWorkbenchFlow(shell: HTMLElement, restoreCallbacks: Array<() => void>) {
  const section = sectionRefs.get('bid');

  if (!section) {
    return;
  }

  const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', section);
  const showcase = section.querySelector<HTMLElement>('.bid-showcase');
  const railItems = gsap.utils.toArray<HTMLElement>('.workbench-rail span', section);
  const sheets = gsap.utils.toArray<HTMLElement>('.document-sheet', section);
  const scanBeam = section.querySelector<HTMLElement>('.document-scan-beam');
  const docLines = gsap.utils.toArray<HTMLElement>('.doc-lines span', section);
  const reviewPanel = section.querySelector<HTMLElement>('.ai-review-panel');
  const reviewScore = reviewPanel?.querySelector<HTMLElement>('strong') ?? null;
  const workflowItems = gsap.utils.toArray<HTMLElement>('.workflow-track span', section);
  const modules = gsap.utils.toArray<HTMLElement>('.module-ribbon article', section);
  const finalScore = reviewScore?.textContent?.trim() ?? '90%';

  restoreTextOnCleanup(reviewScore, restoreCallbacks);

  gsap.set(copyItems, { autoAlpha: 0, x: -28, filter: 'blur(8px)' });
  gsap.set(showcase, { autoAlpha: 0, y: 34, scale: 0.965, filter: 'blur(12px)' });
  gsap.set(railItems, { autoAlpha: 0, x: -34 });
  gsap.set(sheets, {
    autoAlpha: 0,
    y: (index: number) => (index === 0 ? 36 : 54),
    rotation: (index: number) => (index === 0 ? -2 : 6),
    scale: 0.96
  });
  gsap.set(scanBeam, { autoAlpha: 0, yPercent: -130 });
  gsap.set(docLines, { scaleX: 0, transformOrigin: 'left center' });
  gsap.set(reviewPanel, { autoAlpha: 0, scale: 0.86, y: 28, filter: 'blur(8px)' });
  gsap.set(workflowItems, { autoAlpha: 0.32, y: 12, '--track-fill': 0 });
  gsap.set(modules, { autoAlpha: 0, y: 26, rotationX: -18, transformOrigin: '50% 0%' });

  const timeline = gsap.timeline({
    defaults: { ease: 'power3.out' },
    scrollTrigger: {
      id: 'bid-workbench-scan',
      trigger: section,
      scroller: shell,
      start: 'top 68%',
      end: 'center 34%',
      toggleActions: 'play none none reverse',
      invalidateOnRefresh: true,
      refreshPriority: 3
    }
  });

  timeline
    .to(copyItems, { autoAlpha: 1, x: 0, filter: 'blur(0px)', duration: 0.5, stagger: 0.055 }, 0)
    .to(showcase, { autoAlpha: 1, y: 0, scale: 1, filter: 'blur(0px)', duration: 0.58, ease: 'expo.out' }, 0.08)
    .to(railItems, { autoAlpha: 1, x: 0, duration: 0.36, stagger: 0.055 }, 0.26)
    .to(
      sheets,
      {
        autoAlpha: 1,
        y: 0,
        rotation: (index: number) => (index === 0 ? 0 : 3),
        scale: 1,
        duration: 0.54,
        stagger: 0.06,
        ease: 'expo.out'
      },
      0.34
    )
    .to(scanBeam, { autoAlpha: 1, duration: 0.12 }, 0.56)
    .to(scanBeam, { yPercent: 230, duration: 0.56, ease: 'power1.inOut' }, 0.58)
    .to(scanBeam, { autoAlpha: 0, duration: 0.18 }, 1.02)
    .to(docLines, { scaleX: 1, duration: 0.28, stagger: 0.06, ease: 'power2.out' }, 0.76)
    .to(
      reviewPanel,
      { autoAlpha: 1, scale: 1, y: 0, filter: 'blur(0px)', duration: 0.42, ease: 'back.out(1.2)' },
      0.92
    );

  addCountTextTween(timeline, reviewScore, finalScore, 0.96, 0.38);

  timeline
    .to(workflowItems, { autoAlpha: 1, y: 0, '--track-fill': 1, duration: 0.34, stagger: 0.06 }, 1.12)
    .to(modules, { autoAlpha: 1, y: 0, rotationX: 0, duration: 0.42, stagger: 0.045, ease: 'power3.out' }, 1.24);
}

function setupTenderReviewFlow(shell: HTMLElement) {
  const section = sectionRefs.get('tender');

  if (!section) {
    return;
  }

  const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', section);
  const showcase = section.querySelector<HTMLElement>('.tender-showcase');
  const pipelineLine = section.querySelector<HTMLElement>('.tender-pipeline-line');
  const pipelineItems = gsap.utils.toArray<HTMLElement>('.tender-pipeline span:not(.tender-pipeline-line)', section);
  const reviewRows = gsap.utils.toArray<HTMLElement>('.review-row', section);
  const orbit = section.querySelector<HTMLElement>('.score-orbit');
  const orbitLabels = gsap.utils.toArray<HTMLElement>('.score-orbit span', section);
  const modules = gsap.utils.toArray<HTMLElement>('.tender-module-list article', section);

  gsap.set(copyItems, { autoAlpha: 0, x: -24, filter: 'blur(8px)' });
  gsap.set(showcase, { autoAlpha: 0, y: 38, scale: 0.968, filter: 'blur(12px)' });
  gsap.set(pipelineLine, { scaleY: 0, transformOrigin: 'top center' });
  gsap.set(pipelineItems, { autoAlpha: 0, x: -34 });
  gsap.set(reviewRows, { autoAlpha: 0, x: 64, scale: 0.97, filter: 'blur(8px)' });
  gsap.set(orbit, { autoAlpha: 0, scale: 0.72, filter: 'blur(10px)' });
  gsap.set(orbitLabels, { autoAlpha: 0, scale: 0.82 });
  gsap.set(modules, { autoAlpha: 0, y: 28, rotationX: -20, transformOrigin: '50% 0%' });

  const timeline = gsap.timeline({
    defaults: { ease: 'power3.out' },
    scrollTrigger: {
      id: 'tender-review-closure',
      trigger: section,
      scroller: shell,
      start: 'top 68%',
      end: 'center 34%',
      toggleActions: 'play none none reverse',
      invalidateOnRefresh: true,
      refreshPriority: 4
    }
  });

  timeline
    .to(copyItems, { autoAlpha: 1, x: 0, filter: 'blur(0px)', duration: 0.5, stagger: 0.055 }, 0)
    .to(showcase, { autoAlpha: 1, y: 0, scale: 1, filter: 'blur(0px)', duration: 0.58, ease: 'expo.out' }, 0.08)
    .to(pipelineLine, { scaleY: 1, duration: 0.56, ease: 'power2.inOut' }, 0.28)
    .to(pipelineItems, { autoAlpha: 1, x: 0, duration: 0.34, stagger: 0.055 }, 0.36)
    .to(
      reviewRows,
      { autoAlpha: 1, x: 0, scale: 1, filter: 'blur(0px)', duration: 0.44, stagger: 0.08, ease: 'expo.out' },
      0.58
    )
    .to(orbit, { autoAlpha: 1, scale: 1, filter: 'blur(0px)', duration: 0.54, ease: 'back.out(1.18)' }, 0.76)
    .to(orbitLabels, { autoAlpha: 1, scale: 1, duration: 0.26, stagger: 0.055 }, 0.92)
    .to(modules, { autoAlpha: 1, y: 0, rotationX: 0, duration: 0.42, stagger: 0.045 }, 1.06);
}

function setupEvidenceMotion(shell: HTMLElement, restoreCallbacks: Array<() => void>) {
  const valueSection = sectionRefs.get('value');
  const securitySection = sectionRefs.get('security');
  const trustSection = sectionRefs.get('trust');
  const contactSection = sectionRefs.get('contact');

  if (valueSection) {
    const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', valueSection);
    const metricCards = gsap.utils.toArray<HTMLElement>('.metric-card', valueSection);
    const metricValues = metricCards.map(card => card.querySelector<HTMLElement>('strong'));

    metricValues.forEach(value => restoreTextOnCleanup(value, restoreCallbacks));
    gsap.set(copyItems, { autoAlpha: 0, y: 22, filter: 'blur(8px)' });
    gsap.set(metricCards, { autoAlpha: 0, y: 34, rotationX: -18, transformOrigin: '50% 0%' });

    const timeline = gsap.timeline({
      defaults: { ease: 'power3.out' },
      scrollTrigger: {
        id: 'value-evidence-count',
        trigger: valueSection,
        scroller: shell,
        start: 'top 70%',
        end: 'center 40%',
        toggleActions: 'play none none reverse',
        invalidateOnRefresh: true,
        refreshPriority: 5
      }
    });

    timeline
      .to(copyItems, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.46, stagger: 0.055 }, 0)
      .to(metricCards, { autoAlpha: 1, y: 0, rotationX: 0, duration: 0.46, stagger: 0.07, ease: 'power3.out' }, 0.18);

    metricValues.forEach((value, index) => {
      if (!value) {
        return;
      }

      addCountTextTween(timeline, value, value.textContent?.trim() ?? '', 0.34 + index * 0.07, 0.36);
    });
  }

  if (securitySection) {
    const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', securitySection);
    const device = securitySection.querySelector<HTMLElement>('.security-device');
    const cards = gsap.utils.toArray<HTMLElement>('.security-card', securitySection);

    gsap.set(copyItems, { autoAlpha: 0, x: -24, filter: 'blur(8px)' });
    gsap.set(device, { autoAlpha: 0, scale: 0.88, rotateY: -12, filter: 'blur(10px)' });
    gsap.set(cards, { autoAlpha: 0, x: 34, '--shield-fill': 0 });

    gsap
      .timeline({
        defaults: { ease: 'power3.out' },
        scrollTrigger: {
          id: 'security-shield-layer',
          trigger: securitySection,
          scroller: shell,
          start: 'top 70%',
          end: 'center 40%',
          toggleActions: 'play none none reverse',
          invalidateOnRefresh: true,
          refreshPriority: 6
        }
      })
      .to(copyItems, { autoAlpha: 1, x: 0, filter: 'blur(0px)', duration: 0.46, stagger: 0.055 }, 0)
      .to(device, { autoAlpha: 1, scale: 1, rotateY: 0, filter: 'blur(0px)', duration: 0.6, ease: 'expo.out' }, 0.16)
      .to(cards, { autoAlpha: 1, x: 0, '--shield-fill': 1, duration: 0.42, stagger: 0.075 }, 0.38);
  }

  if (trustSection) {
    const copyItems = gsap.utils.toArray<HTMLElement>('.section-copy > *', trustSection);
    const photo = trustSection.querySelector<HTMLElement>('.company-photo-card');
    const stats = gsap.utils.toArray<HTMLElement>('.company-stat-strip strong', trustSection);
    const cards = gsap.utils.toArray<HTMLElement>('.trust-card', trustSection);

    stats.forEach(stat => restoreTextOnCleanup(stat, restoreCallbacks));
    gsap.set(copyItems, { autoAlpha: 0, y: 22, filter: 'blur(8px)' });
    gsap.set(photo, { autoAlpha: 0, y: 38, scale: 0.97, filter: 'blur(10px)' });
    gsap.set(cards, { autoAlpha: 0, y: 28, rotation: (index: number) => [-2, 1.5, -1, 2][index] ?? 0 });

    const timeline = gsap.timeline({
      defaults: { ease: 'power3.out' },
      scrollTrigger: {
        id: 'trust-archive-wall',
        trigger: trustSection,
        scroller: shell,
        start: 'top 70%',
        end: 'center 40%',
        toggleActions: 'play none none reverse',
        invalidateOnRefresh: true,
        refreshPriority: 7
      }
    });

    timeline
      .to(copyItems, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.46, stagger: 0.055 }, 0)
      .to(photo, { autoAlpha: 1, y: 0, scale: 1, filter: 'blur(0px)', duration: 0.58, ease: 'expo.out' }, 0.16)
      .to(cards, { autoAlpha: 1, y: 0, rotation: 0, duration: 0.42, stagger: 0.065 }, 0.36);

    stats.forEach((stat, index) => {
      addCountTextTween(timeline, stat, stat.textContent?.trim() ?? '', 0.38 + index * 0.06, 0.34);
    });
  }

  if (contactSection) {
    const panel = contactSection.querySelector<HTMLElement>('.contact-panel');
    const copyItems = gsap.utils.toArray<HTMLElement>('.contact-panel-copy > *', contactSection);
    const actions = gsap.utils.toArray<HTMLElement>('.contact-action', contactSection);
    const metaItems = gsap.utils.toArray<HTMLElement>('.contact-company-meta span', contactSection);

    gsap.set(panel, { autoAlpha: 0, scale: 0.97, y: 34, filter: 'blur(12px)' });
    gsap.set(copyItems, { autoAlpha: 0, y: 18 });
    gsap.set(actions, { autoAlpha: 0, x: (index: number) => [-34, 34][index] ?? 0, scale: 0.98 });
    gsap.set(metaItems, { autoAlpha: 0, y: 10 });

    gsap
      .timeline({
        defaults: { ease: 'power3.out' },
        scrollTrigger: {
          id: 'contact-converge',
          trigger: contactSection,
          scroller: shell,
          start: 'top 76%',
          end: 'center 48%',
          toggleActions: 'play none none reverse',
          invalidateOnRefresh: true,
          refreshPriority: 8
        }
      })
      .to(panel, { autoAlpha: 1, scale: 1, y: 0, filter: 'blur(0px)', duration: 0.58, ease: 'expo.out' }, 0)
      .to(copyItems, { autoAlpha: 1, y: 0, duration: 0.4, stagger: 0.055 }, 0.14)
      .to(actions, { autoAlpha: 1, x: 0, scale: 1, duration: 0.4, stagger: 0.07 }, 0.36)
      .to(metaItems, { autoAlpha: 1, y: 0, duration: 0.32, stagger: 0.055 }, 0.56);
  }
}

function setupStoryMotion() {
  const shell = landingShell.value;

  if (!shell) {
    return;
  }

  storyMotionMedia?.revert();
  storyMotionMedia = gsap.matchMedia();

  storyMotionMedia.add('(min-width: 1121px) and (prefers-reduced-motion: no-preference)', () => {
    const restoreCallbacks: Array<() => void> = [];
    const context = gsap.context(() => {
      setupHeroIntro(restoreCallbacks);
      setupSolutionFlow(shell);
      setupBidWorkbenchFlow(shell, restoreCallbacks);
      setupTenderReviewFlow(shell);
      setupEvidenceMotion(shell, restoreCallbacks);
      requestAnimationFrame(() => ScrollTrigger.refresh());
      void document.fonts.ready.then(() => ScrollTrigger.refresh());
    }, shell);

    return () => {
      restoreCallbacks.forEach(callback => callback());
      context.revert();
    };
  });
}

function setupPainScrollytelling() {
  const shell = landingShell.value;
  const painSection = sectionRefs.get('pain');

  if (!shell || !painSection) {
    return;
  }

  painMotionMedia?.revert();
  painMotionMedia = gsap.matchMedia();

  painMotionMedia.add('(min-width: 1121px) and (prefers-reduced-motion: no-preference)', () => {
    const heroSection = sectionRefs.get('hero');
    const cards = gsap.utils.toArray<HTMLElement>('.pain-card', painSection);
    const copyItems = gsap.utils.toArray<HTMLElement>('.pain-copy > *', painSection);
    const cardContent = cards.flatMap(card => gsap.utils.toArray<HTMLElement>('.pain-card-copy', card));
    const heroTargets = heroSection
      ? gsap.utils.toArray<HTMLElement>('.gallery-copy, .hero-data-strip', heroSection)
      : [];

    if (!cards.length || !copyItems.length) {
      return;
    }

    const cardLayout = [
      { x: -126, y: -152, rotation: -5.8, scale: 1, zIndex: 1 },
      { x: 52, y: -44, rotation: 3.4, scale: 0.985, zIndex: 2 },
      { x: -72, y: 62, rotation: -2.2, scale: 0.97, zIndex: 3 },
      { x: 98, y: 164, rotation: 4.8, scale: 0.95, zIndex: 4 }
    ];

    if (heroTargets.length) {
      gsap.to(heroTargets, {
        autoAlpha: 0.42,
        y: -86,
        filter: 'blur(10px)',
        ease: 'none',
        scrollTrigger: {
          id: 'hero-to-pain-recede',
          trigger: painSection,
          scroller: shell,
          start: 'top bottom',
          end: 'top top',
          scrub: 0.7,
          invalidateOnRefresh: true,
          refreshPriority: 0
        }
      });
    }

    gsap.set(copyItems, {
      autoAlpha: 0.76,
      y: 12,
      filter: 'blur(1.5px)'
    });
    gsap.set(cards, {
      autoAlpha: 0.66,
      xPercent: -50,
      yPercent: -50,
      x: (index: number) => [-92, -42, 12, 66][index] ?? 0,
      y: (index: number) => -48 + index * 21,
      z: (index: number) => -120 + index * 34,
      zIndex: (index: number) => index + 1,
      rotationX: -54,
      rotation: (index: number) => [-7, -2.8, 1.8, 6.2][index] ?? 0,
      scale: (index: number) => 0.9 + index * 0.012,
      transformOrigin: '50% 0%',
      transformStyle: 'preserve-3d',
      force3D: true
    });
    gsap.set(cardContent, {
      autoAlpha: 0.12,
      y: 14
    });

    const timeline = gsap.timeline({
      defaults: { ease: 'none' },
      scrollTrigger: {
        id: 'pain-card-stack-scrolly',
        trigger: painSection,
        scroller: shell,
        start: 'top top',
        end: () => `+=${Math.max(shell.clientHeight * 1.65, 1160)}`,
        pin: true,
        scrub: 0.3,
        anticipatePin: 1,
        invalidateOnRefresh: true,
        refreshPriority: 1,
        onEnter: () => {
          activeSection.value = 'pain';
        },
        onEnterBack: () => {
          activeSection.value = 'pain';
        }
      }
    });

    timeline
      .to(copyItems, { autoAlpha: 1, y: 0, filter: 'blur(0px)', duration: 0.18, stagger: 0.035 }, 0)
      .to(cards, { autoAlpha: 1, duration: 0.08, stagger: 0.018 }, 0)
      .to(
        cards,
        { rotationX: -22, y: (index: number) => -22 + index * 15, z: -46, scale: 0.94, duration: 0.14, stagger: 0.02 },
        0.04
      );

    cards.forEach((card, index) => {
      const layout = cardLayout[index] ?? cardLayout[cardLayout.length - 1];
      const start = 0.24 + index * 0.115;
      const content = gsap.utils.toArray<HTMLElement>('.pain-card-copy', card);

      timeline
        .to(
          card,
          {
            x: layout.x,
            y: layout.y,
            z: 0,
            zIndex: layout.zIndex,
            rotationX: 0,
            rotation: layout.rotation,
            scale: layout.scale,
            duration: 0.28
          },
          start
        )
        .to(content, { autoAlpha: 1, y: 0, duration: 0.16, stagger: 0.018 }, start + 0.075);
    });

    timeline.to(cards, { y: (index: number) => (cardLayout[index] ?? cardLayout[0]).y + 12, duration: 0.12 }, 0.83);

    requestAnimationFrame(() => ScrollTrigger.refresh());
    void document.fonts.ready.then(() => ScrollTrigger.refresh());
  });
}

onMounted(async () => {
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
    { threshold: [0.28, 0.45, 0.65], rootMargin: '-8% 0px -18% 0px' }
  );

  sectionRefs.forEach(element => sectionObserver?.observe(element));

  await nextTick();
  setupStoryCanvas();
  setupStoryMotion();
  setupPainScrollytelling();
});

onUnmounted(() => {
  sectionObserver?.disconnect();
  sectionObserver = null;
  painMotionMedia?.revert();
  painMotionMedia = null;
  storyMotionMedia?.revert();
  storyMotionMedia = null;
  cleanupStoryCanvas?.();
  cleanupStoryCanvas = null;
});
</script>

<template>
  <main ref="landingShell" class="landing-shell" :data-active-section="activeSection">
    <div class="ambient-layer" aria-hidden="true">
      <span class="ambient-surface" />
      <span class="ambient-sheen" />
      <span class="ambient-grid" />
      <span class="story-paper-field" />
      <span class="story-thread-field" />
      <span class="story-depth-field" />
    </div>

    <header class="site-nav">
      <button class="brand-mark" type="button" @click="scrollToSection('hero')">
        <span class="brand-symbol" aria-hidden="true">
          <img src="/brand/bidflow-logo.svg?v=20260703b" alt="" />
        </span>
        <span class="brand-wordmark">
          <strong>智标宝</strong>
          <small>BidFlow AI</small>
        </span>
      </button>

      <nav class="nav-links" aria-label="Main navigation" :style="navIndicatorStyle">
        <button
          v-for="item in navItems"
          :key="item.key"
          type="button"
          :class="{ active: activeSection === item.key }"
          @click="scrollToSection(item.key)"
        >
          {{ item.label }}
        </button>
      </nav>

      <div class="nav-actions">
        <button class="platform-link" type="button" @click="openUrlOrModal(feedbackUrl, 'feedback')">客户反馈</button>
        <LiquidGlassButton label="预约演示" size="nav" @click="openUrlOrModal(demoUrl, 'demo')" />
      </div>
    </header>

    <aside class="section-indicator" aria-label="Section progress">
      <button
        v-for="item in sections"
        :key="item.key"
        type="button"
        :class="{ active: activeSection === item.key }"
        :aria-label="item.label"
        @click="scrollToSection(item.key)"
      >
        <i />
        <strong>{{ item.label }}</strong>
      </button>
    </aside>

    <section
      :ref="element => setSectionRef('hero', element)"
      class="snap-section hero-section gallery-hero"
      :class="{ 'is-active': isActive('hero') }"
      data-section="hero"
    >
      <div class="gallery-backdrop" aria-hidden="true">
        <img src="/media/hero-gallery.png" alt="" />
      </div>
      <div class="gallery-vignette" aria-hidden="true" />
      <div class="gallery-motion" aria-hidden="true">
        <span class="motion-sweep" />
        <span class="motion-thread thread-one" />
        <span class="motion-thread thread-two" />
        <span class="motion-thread thread-three" />
      </div>

      <div class="hero-copy gallery-copy reveal-stack">
        <h1>
          <span class="hero-title-line">
            智标宝
            <em class="hero-title-product">BidFlow AI</em>
          </span>
          <span class="hero-title-kicker">招投标全流程 AI 作业平台</span>
        </h1>
        <p class="hero-description">连接招标策划、投标响应、智能评审与风险审查，让标书更快生成，让风险更早暴露。</p>
        <p class="hero-proof">面向招采端、投标端和企业级安全部署的 AI 作业系统。</p>
        <div class="hero-actions">
          <LiquidGlassButton label="预约演示" size="hero" @click="openUrlOrModal(demoUrl, 'demo')" />
          <div class="platform-entry-group" aria-label="平台入口">
            <button type="button" class="platform-entry-button" @click="openUrlOrModal(tenderLoginUrl, 'contact')">
              <span>进入招采平台</span>
            </button>
            <button type="button" class="platform-entry-button" @click="openUrlOrModal(bidLoginUrl, 'contact')">
              <span>进入投标平台</span>
            </button>
          </div>
        </div>
      </div>

      <div class="hero-data-strip" aria-label="核心价值">
        <span>解标</span>
        <i />
        <span>写标</span>
        <i />
        <span>查重</span>
        <i />
        <span>审标</span>
        <i />
        <span>评审</span>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('pain', element)"
      class="snap-section pain-section"
      :class="{ 'is-active': isActive('pain') }"
      data-section="pain"
    >
      <div class="section-copy pain-copy">
        <p class="eyebrow">Why 智标宝</p>
        <h2>招投标团队真正头疼的，不只是写标书</h2>
        <p>流程长、资料散、风险隐蔽、审查繁重。智标宝把这些重复劳动和关键风险提前收束到一套 AI 作业链里。</p>
      </div>

      <div class="pain-card-stack" aria-label="客户痛点">
        <article v-for="(point, index) in painPoints" :key="point.title" class="insight-card pain-card">
          <span class="pain-card-copy">{{ point.label }}</span>
          <h3 class="pain-card-copy">{{ point.title }}</h3>
          <p class="pain-card-copy">{{ point.description }}</p>
          <b aria-hidden="true">{{ String(index + 1).padStart(2, '0') }}</b>
        </article>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('solution', element)"
      class="snap-section solution-section"
      :class="{ 'is-active': isActive('solution') }"
      data-section="solution"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">One Engine. Two Workflows.</p>
        <h2>一套 BidFlow AI Engine，贯穿招采与投标两条作业链</h2>
        <p>智标宝不是单个工具，而是把招采端、投标端和底层 AI 能力连接成一个可持续复用的业务中枢。</p>
      </div>

      <div class="solution-stage reveal-item">
        <article
          v-for="(node, index) in solutionNodes"
          :key="node.title"
          class="solution-node"
          :class="`node-${index}`"
        >
          <span>{{ node.label }}</span>
          <h3>{{ node.title }}</h3>
          <p>{{ node.description }}</p>
        </article>
        <div class="stage-pulse stage-pulse-left" aria-hidden="true" />
        <div class="stage-pulse stage-pulse-right" aria-hidden="true" />
        <div class="stage-line stage-line-left" aria-hidden="true" />
        <div class="stage-line stage-line-right" aria-hidden="true" />
      </div>
    </section>

    <section
      :ref="element => setSectionRef('bid', element)"
      class="snap-section suite-section bid-section"
      :class="{ 'is-active': isActive('bid') }"
      data-section="bid"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">Bid Suite</p>
        <h2>投标平台：从读懂招标文件，到交付高质量标书</h2>
        <p>围绕“解、写、查、审”形成投标闭环，把历史经验、企业资料和 AI 审查能力沉淀为长期资产。</p>
        <LiquidGlassButton label="进入投标平台" size="hero" @click="openUrlOrModal(bidLoginUrl, 'contact')" />
      </div>

      <div class="product-showcase bid-showcase reveal-item">
        <div class="showcase-window bid-workbench">
          <div class="showcase-toolbar">
            <span class="toolbar-dot" />
            <span class="toolbar-dot" />
            <span class="toolbar-dot" />
            <strong>Bid Suite / 智能投标工作台</strong>
          </div>

          <div class="workbench-body">
            <aside class="workbench-rail">
              <span v-for="source in bidAssetSources" :key="source">{{ source }}</span>
            </aside>

            <div class="document-stage">
              <article class="document-sheet document-sheet-main">
                <p>招标文件解析</p>
                <h3>资格 / 商务 / 技术 / 评分要求</h3>
                <div class="doc-lines" aria-hidden="true">
                  <span />
                  <span />
                  <span />
                </div>
              </article>
              <article class="document-sheet document-sheet-back" aria-hidden="true" />
              <span class="document-scan-beam" aria-hidden="true" />
              <div class="ai-review-panel">
                <span>AI 审查结果</span>
                <strong>90%</strong>
                <p>应答缺失项检测准确率</p>
              </div>
            </div>
          </div>

          <div class="workflow-track" aria-label="投标作业流程">
            <span v-for="step in bidWorkbenchSteps" :key="step">{{ step }}</span>
          </div>
        </div>

        <div class="module-ribbon">
          <article v-for="capability in bidCapabilities" :key="capability.title">
            <span>{{ capability.label }}</span>
            <strong>{{ capability.title }}</strong>
          </article>
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('tender', element)"
      class="snap-section suite-section tender-section"
      :class="{ 'is-active': isActive('tender') }"
      data-section="tender"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">Tender Suite</p>
        <h2>招采平台：让策划、编制与评审审查形成闭环</h2>
        <p>从招标需求到评标规则，从串标审查到智能评分，帮助招采团队获得可追溯、可复核的工作结果。</p>
        <LiquidGlassButton label="进入招采平台" size="hero" @click="openUrlOrModal(tenderLoginUrl, 'contact')" />
      </div>

      <div class="product-showcase tender-showcase reveal-item">
        <div class="showcase-window tender-console">
          <div class="showcase-toolbar">
            <span class="toolbar-dot" />
            <span class="toolbar-dot" />
            <span class="toolbar-dot" />
            <strong>Tender Suite / 招采评审控制台</strong>
          </div>

          <div class="tender-console-body">
            <div class="tender-pipeline" aria-label="招采作业流程">
              <span class="tender-pipeline-line" aria-hidden="true" />
              <span v-for="step in tenderPipeline" :key="step">{{ step }}</span>
            </div>

            <div class="review-desk">
              <article v-for="row in tenderReviewRows" :key="row.title" class="review-row">
                <span>{{ row.state }}</span>
                <strong>{{ row.title }}</strong>
                <p>{{ row.meta }}</p>
              </article>
            </div>

            <div class="score-orbit" aria-label="智能评分">
              <strong>AI</strong>
              <span>规则抽取</span>
              <span>风险审查</span>
              <span>评分辅助</span>
            </div>
          </div>
        </div>

        <div class="tender-module-list">
          <article v-for="capability in tenderCapabilities" :key="capability.title">
            <span>{{ capability.label }}</span>
            <strong>{{ capability.title }}</strong>
            <p>{{ capability.description }}</p>
          </article>
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('value', element)"
      class="snap-section value-section"
      :class="{ 'is-active': isActive('value') }"
      data-section="value"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">Measured Value</p>
        <h2>把重复劳动交给 AI，把关键判断留给团队</h2>
        <p>以下数据来自产品资料和典型案例表达，正式沟通时可按客户行业与部署方式进一步确认。</p>
      </div>

      <div class="metric-grid">
        <article v-for="metric in metrics" :key="metric.label" class="metric-card reveal-item">
          <strong>{{ metric.value }}</strong>
          <span>{{ metric.label }}</span>
          <p>{{ metric.description }}</p>
        </article>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('security', element)"
      class="snap-section security-section"
      :class="{ 'is-active': isActive('security') }"
      data-section="security"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">Enterprise Ready</p>
        <h2>面向企业级招投标场景的安全部署能力</h2>
        <p>面向集团、国企与敏感业务场景，智标宝可以围绕本地部署、权限分级、国产算力和全流程留痕落地。</p>
      </div>

      <div class="security-layout">
        <div class="security-device reveal-item" aria-hidden="true">
          <span class="device-light" />
          <strong>BidFlow AI<br />Secure Node</strong>
          <i>本地私有化 / 软硬一体 / 数据留痕</i>
        </div>
        <div class="security-list">
          <article v-for="item in securityItems" :key="item.title" class="security-card reveal-item">
            <span>{{ item.label }}</span>
            <h3>{{ item.title }}</h3>
            <p>{{ item.description }}</p>
          </article>
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('trust', element)"
      class="snap-section trust-section"
      :class="{ 'is-active': isActive('trust') }"
      data-section="trust"
    >
      <div class="section-copy reveal-item">
        <p class="eyebrow">Company Trust</p>
        <h2>来自炫佳科技的 AI 工业化能力</h2>
        <p>智标宝由炫佳科技打造，依托长期产业沉淀、AI 技术投入与行业场景落地能力，支撑企业级招投标作业平台持续演进。</p>
      </div>

      <div class="trust-proof reveal-item">
        <div class="company-photo-card">
          <img src="/media/company-campus-ai.png" alt="炫佳科技办公楼外观" />
          <div class="company-logo-badge" aria-label="炫佳科技">
            <img src="/media/company-logo.png" alt="炫佳科技" />
          </div>
          <div class="company-stat-strip" aria-label="公司关键数据">
            <span v-for="stat in companyStats" :key="stat.label">
              <strong>{{ stat.value }}</strong>
              <small>{{ stat.label }}</small>
            </span>
          </div>
        </div>
        <div class="trust-grid">
          <article v-for="card in trustCards" :key="card.title" class="trust-card reveal-item">
            <span>{{ card.label }}</span>
            <h3>{{ card.title }}</h3>
            <p>{{ card.description }}</p>
          </article>
        </div>
      </div>
    </section>

    <section
      :ref="element => setSectionRef('contact', element)"
      class="snap-section contact-section"
      :class="{ 'is-active': isActive('contact') }"
      data-section="contact"
    >
      <div class="contact-panel reveal-item">
        <div class="contact-panel-copy">
          <p class="eyebrow">Contact / Feedback</p>
          <h2>从一次演示开始，确认智标宝如何落到你的业务里</h2>
          <p>告诉我们你的业务场景，我们会结合招采、投标、评审和审查流程，演示平台能力、部署方式与落地路径。</p>
        </div>

        <div class="contact-action-grid">
          <button
            v-for="action in contactActions"
            :key="action.title"
            type="button"
            class="contact-action"
            @click="openUrlOrModal('', action.kind)"
          >
            <span>{{ action.label }}</span>
            <strong>{{ action.title }}</strong>
            <small>{{ action.description }}</small>
          </button>
        </div>

        <div class="contact-company-meta" aria-label="公司信息">
          <img src="/media/company-logo.png" alt="炫佳科技" />
          <span>南京炫佳网络科技有限公司</span>
          <span>南京江北新区</span>
          <span>智标宝企业级招投标 AI 平台咨询</span>
        </div>
      </div>
    </section>

    <div v-if="showModal" class="contact-modal" role="dialog" aria-modal="true" @click.self="closeModal">
      <div class="modal-card" :class="`modal-${modalKind}`">
        <button class="modal-close" type="button" aria-label="Close" @click="closeModal">×</button>
        <template v-if="modalSubmitted">
          <p class="eyebrow">{{ currentModalCopy.eyebrow }}</p>
          <h2>{{ currentModalCopy.successTitle }}</h2>
          <p>{{ currentModalCopy.successText }}</p>
          <button class="modal-submit" type="button" @click="closeModal">完成</button>
        </template>
        <template v-else>
          <p class="eyebrow">{{ currentModalCopy.eyebrow }}</p>
          <h2>{{ currentModalCopy.title }}</h2>
          <p>{{ currentModalCopy.description }}</p>

          <form class="modal-form" @submit.prevent="submitModal">
            <template v-if="modalKind === 'feedback'">
              <label>
                <span>反馈类型</span>
                <select v-model="feedbackForm.type">
                  <option>产品建议</option>
                  <option>问题反馈</option>
                  <option>演示体验</option>
                  <option>行业场景补充</option>
                </select>
              </label>
              <label>
                <span>联系方式</span>
                <input v-model="feedbackForm.contact" type="text" placeholder="手机号 / 邮箱 / 企业微信" />
              </label>
              <label>
                <span>反馈主题</span>
                <input v-model="feedbackForm.title" type="text" placeholder="例如：招标文件解析结果建议" />
              </label>
              <label class="modal-field-wide">
                <span>反馈内容</span>
                <textarea
                  v-model="feedbackForm.content"
                  rows="5"
                  placeholder="请描述你遇到的问题、建议或希望补充的业务场景。"
                />
              </label>
            </template>

            <template v-else>
              <label>
                <span>姓名</span>
                <input v-model="demoForm.name" type="text" placeholder="请输入姓名" />
              </label>
              <label>
                <span>公司 / 组织</span>
                <input v-model="demoForm.company" type="text" placeholder="请输入公司名称" />
              </label>
              <label>
                <span>联系方式</span>
                <input v-model="demoForm.contact" type="text" placeholder="手机号 / 邮箱 / 企业微信" />
              </label>
              <label>
                <span>你的角色</span>
                <input v-model="demoForm.role" type="text" placeholder="例如：招采负责人 / 投标负责人" />
              </label>
              <label>
                <span>关注平台</span>
                <select v-model="demoForm.platform">
                  <option>招采平台 + 投标平台</option>
                  <option>投标平台</option>
                  <option>招采平台</option>
                  <option>私有化 / 软硬一体部署</option>
                </select>
              </label>
              <label class="modal-field-wide">
                <span>希望沟通的场景</span>
                <textarea
                  v-model="demoForm.scene"
                  rows="4"
                  placeholder="例如：集团多子公司投标协同、评标合规审查、私有化部署等。"
                />
              </label>
            </template>

            <button class="modal-submit" type="submit">{{ currentModalCopy.submitLabel }}</button>
          </form>
        </template>
      </div>
    </div>
  </main>
</template>
