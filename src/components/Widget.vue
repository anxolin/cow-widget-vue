<template>
  <div class="widget__container">
    <div id="cow_widget" ref="cow_widget"></div>
  </div>
</template>

<script setup lang="ts">
import {
  createCowSwapWidget,
  type CowSwapWidgetParams,
  TradeType,
  type CowSwapWidgetProps,
  type CowSwapWidgetHandler,
} from '@cowprotocol/widget-lib'
import { CowWidgetEvents, type CowWidgetEventListeners } from '@cowprotocol/events'
import { onMounted, ref, type PropType } from 'vue'

const FEE_RECIPIENT = '0xCd777a10502256dB93c2b0A8e8F64a5174c6cbDa'

const props = defineProps({
  sellToken: {
    type: String as PropType<string | undefined>,
    required: false,
  },
  sellAmount: {
    type: String as PropType<string | undefined>,
    required: false,
  },
  buyToken: {
    type: String as PropType<string | undefined>,
    required: false,
  },
  buyAmount: {
    type: String as PropType<string | undefined>,
    required: false,
  },
})
const emit = defineEmits(['confirmed'])
const cow_widget = ref<HTMLDivElement | null>(null)
const widget = ref<CowSwapWidgetHandler | null>(null)

const BASE_URL = 'https://swap-dev-git-allow-change-network-standalone-widget-cowswap.vercel.app' // Use 'https://swap.cow.fi'

const params: CowSwapWidgetParams = {
  appCode: 'Milk Road Swap', // Name of your app (max 50 characters)
  // baseUrl: 'https://swap.cow.fi',
  baseUrl: BASE_URL,
  //'min-width': '100%', // Min width in pixels (or 100% to use all available space)
  width: '100%', // Width in pixels (or 100% to use all available space)
  height: '640px',
  chainId: [1, 42161, 8453],
  tokenLists: [
    // All default enabled token lists. Also see https://tokenlists.org
    'https://files.cow.fi/tokens/CoinGecko.json',
    'https://files.cow.fi/tokens/CowSwap.json',
  ],
  tradeType: TradeType.SWAP, // swap, limit or advanced
  sell: {
    // Sell token. Optionally add amount for sell orders
    asset: props.sellToken ?? '',
    amount: props.sellAmount,
  },

  buy: {
    // Buy token. Optionally add amount for buy orders
    asset: props.buyToken ?? '',
    amount: props.buyAmount,
  },
  forcedOrderDeadline: {
    advanced: 2,
  },
  enabledTradeTypes: [
    // swap, limit, advanced, yield
    TradeType.SWAP,
  ],
  theme: {
    baseTheme: 'dark',
    primary: '#42addb',
    paper: '#f7f5ed',
    text: '#000000',
    background: '#f7f5ed',
    danger: '#000000',
    warning: '#000000',
    alert: '#000000',
    success: '#000000',
    info: '#000000',
  },
  standaloneMode: true,
  disableToastMessages: false,
  disableProgressBar: false,

  // Partner fee, in Basis Points (BPS) and a receiver address
  partnerFee: {
    bps: 15,
    recipient: FEE_RECIPIENT,
  },
  hideBridgeInfo: false,
  hideOrdersTable: false,
  images: {},
  sounds: {},
  customTokens: [
    {
      name: 'Virtual Protocol',
      symbol: 'VIRTUAL',
      decimals: 9,
      logoURI: 'https://ipfs.io/ipfs/bafkreifbjxsikdmuhk5qi6krg3sqk2fcqvqlcffrc44kg7ecfkmwhz6s5i',
      chainId: 1,
      address: '0x44ff8620b8cA30902395A7bD3F2407e1A091BF73',
    },
  ],
}

// Listeners
const listeners: CowWidgetEventListeners = [
  {
    event: CowWidgetEvents.ON_CHANGE_TRADE_PARAMS,
    handler: (event) => {
      console.log('[dapp] New event:Trade params changed', event)

      // TODO: Implement your own fee logic here
      // widget.value?.updateParams({
      //   ...params,
      //   partnerFee: {
      //     bps: Math.floor(Math.random() * 10) + 10,
      //     recipient: FEE_RECIPIENT,
      //   },
      // })
    },
  },
  {
    event: CowWidgetEvents.ON_FULFILLED_ORDER,
    handler: (event) => {
      console.log('[dapp] New event: Order fulfilled', event)
      emit('confirmed', event.order.uid, 'Ethereum', 'ETH', 'VIRTUAL')
    },
  },
]

/**
 * Debug messages between dApp and iframe
 */
function debugIframe() {
  window.addEventListener(
    'message',
    (event) => {
      if (event.origin !== BASE_URL) return

      console.log('[dapp:Parent] Message:', event.data)
    },
    false,
  )

  const iframe = document.querySelector('iframe')
  if (!iframe) return

  iframe.addEventListener(
    'message',
    (event) => {
      console.log('[dapp:Iframe] Message:', event)
    },
    false,
  )
}

// Apply the widget to the div element
onMounted(() => {
  console.log('[dapp] onMounted', params)
  if (!cow_widget.value) return

  const widgetProps: CowSwapWidgetProps = {
    params,
    listeners,
  }

  widget.value = createCowSwapWidget(cow_widget.value, widgetProps)

  // This update of params is not needed!
  // widget.value.updateParams(params)

  // Debug iframe
  debugIframe()
})
</script>

<style>
.widget__container {
  height: auto;
  min-width: 400px;
  /* width: 100%; */
}
</style>
