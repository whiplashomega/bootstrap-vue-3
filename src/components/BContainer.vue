<script lang="ts">
import {Breakpoint} from '../types'
import {
  ComponentPublicInstance,
  computed,
  defineComponent,
  getCurrentInstance,
  h,
  onMounted,
  PropType,
  provide,
  Ref,
  ref,
  VNode,
} from 'vue'
import {ToastInstance, useToast} from './BToast/plugin'
import BToaster from './BToast/BToaster.vue'
import Position from '../types/position'
export default defineComponent({
  name: 'BContainer',
  props: {
    fluid: {type: [Boolean, String] as PropType<boolean | Breakpoint>, default: false},
    toast: {type: Object},
    position: {type: String as PropType<Position>, required: false},
  },
  setup(props, {slots, expose}) {
    const container = ref()
    let toastInstance: ToastInstance | undefined

    const classes = computed(() => ({
      container: !props.fluid,
      [`container-fluid`]: typeof props.fluid === 'boolean' && props.fluid,
      [`container-${props.fluid}`]: typeof props.fluid === 'string',
    }))

    onMounted(() => {
      if (props.toast) {
        // toastInstance.setVmContainer(container)
      }
    })

    // let this be the container for the toast
    if (props.toast) {
      toastInstance = useToast({container, root: props.toast.root})
      expose({
        // ...toastInstance?.useMethods,
      })
    }

    return () => {
      const subContainers: Array<VNode> = []

      toastInstance?.containerPositions.value.forEach((position) => {
        subContainers.push(h(BToaster, {instance: toastInstance, position}))
      })

      return h('div', {class: [classes.value, props.position], ref: container}, [
        ...subContainers,
        slots.default?.(),
      ])
    }
  },
  methods: {},
})
</script>
