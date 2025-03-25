<script lang="ts">
  import { classNames } from '../../utils';
  let { class: className = undefined, ...rest } = $props();
</script>

<li class={ classNames( 'splide__slide', className ) } { ...rest }>
  <slot/>
</li>