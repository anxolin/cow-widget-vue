<template>
  <div class="widget__container">
    <div id="cow_widget" ref="cow_widget"></div>
  </div>
</template>

<script setup lang="ts">
import { createCowSwapWidget, type CowSwapWidgetParams, TradeType } from '@cowprotocol/widget-lib'
import { CowWidgetEvents, type CowWidgetEventListeners } from '@cowprotocol/events'
import { onMounted, ref, type PropType } from 'vue'

const props = defineProps({
  providedAmountIn: {
    type: String as PropType<string>,
    default: '0',
  },
  providedAmountOut: {
    type: String as PropType<string>,
    default: '0',
  },
  providedAssetIn: {
    type: String as PropType<string>,
    default: '',
  },
  providedAssetOut: {
    type: String as PropType<string>,
    default: '',
  },
})
const emit = defineEmits(['confirmed'])
const cow_widget = ref<HTMLDivElement | null>(null)
const params: CowSwapWidgetParams = {
  appCode: 'Milk Road Swap', // Name of your app (max 50 characters)
  baseUrl: 'https://swap-dev-git-allow-change-network-standalone-widget-cowswap.vercel.app',
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
    asset: props.providedAssetIn,
    amount: props.providedAmountIn,
  },
  buy: {
    // Buy token. Optionally add amount for buy orders
    asset: props.providedAssetOut,
    amount: props.providedAmountOut,
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
  partnerFee: {
    // Partner fee, in Basis Points (BPS) and a receiver address
    bps: 15,
    recipient: {
      '1': '0xCd777a10502256dB93c2b0A8e8F64a5174c6cbDa',
      '8453': '0xCd777a10502256dB93c2b0A8e8F64a5174c6cbDa',
      '42161': '0xCd777a10502256dB93c2b0A8e8F64a5174c6cbDa',
    },
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
      console.log('Trade params changed', event)
    },
  },
  {
    event: CowWidgetEvents.ON_FULFILLED_ORDER,
    handler: (event) => {
      console.log('Order fulfilled', event)
      emit('confirmed', event.order.uid, 'Ethereum', 'ETH', 'VIRTUAL')
    },
  },
]
// Apply the widget to the div element
onMounted(() => {
  if (!cow_widget.value) return

  createCowSwapWidget(cow_widget.value, { params, listeners })
})
</script>

<style>
.widget__container {
  height: auto;
  min-width: 400px;
  /* width: 100%; */
}
</style>
