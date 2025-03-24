<script >
  import { classNames } from '../../utils';
  let { class: className = undefined, ...rest } = $props();
</script>

<div class={ classNames( 'splide__track', className ) } { ...rest }>
  <ul class="splide__list">
    <slot/>
  </ul>
</div>
