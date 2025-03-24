<script>

  import { classNames, getSlides, isEqualDeep, isEqualShallow, merge } from '../../utils';
  import { Splide } from '@splidejs/splide';
  import {createEventDispatcher, onMount } from 'svelte';
  import { bind } from './bind';
  import { SplideTrack } from '..';
  
  /**
    * Export attributes
  */
  let { class: className = undefined, options = {}, splide = undefined, extensions = undefined, transition = undefined, hasTrack = true, ...rest } = $props();
  
  /**
   * A dispatcher function.
   * The `createEventDispatcher` type assertion does not accept a type alias.
   * If specified, the svelte kit fails to generate a type of `events` and it will be `CustomEvent<any>`.
   * Also, the svelte action does not provide the way to specify event types.
   */
  const dispatch = createEventDispatcher();
  /**
   * The root element.
   */
  let root;
  /**
   * Holds the previous slide elements.
   */
  let prevSlides;
  /**
   * Holds the previous options.
   */
  let prevOptions = merge({}, options);
  /**
   * Updates splide options only when they have difference with previous options.
   */
   $effect(()=>{
    if (splide && !isEqualDeep(prevOptions, options)) {
        splide.options = options;
        prevOptions.set(merge({}, options)); // Met à jour `prevOptions`
      }
   })
  
  onMount(() => {
      splide = new Splide(root, options);
      bind(splide, dispatch);
      splide.mount(extensions, transition);
      prevSlides = getSlides(splide);
      return () => splide.destroy();
  });
  
  
  $effect.pre(() => {
      if (splide) {
          const newSlides = getSlides(splide);
          if (!isEqualShallow(prevSlides, newSlides)) {
              splide.refresh();
              prevSlides = newSlides.slice();
          }
      }
  });
  /**
   * Moves the slider by the specified control.
   *
   * @param control - A control pattern.
   */
  export function go(control) {
      splide?.go(control);
  }
  /**
   * Syncs the slider with another Splide.
   *
   * @param target - A target splide instance to sync with.
   */
  export function sync(target) {
      splide?.sync(target);
  }
  </script>
  
  <svelte:options accessors/>
  
  <div
    class={ classNames( 'splide', className ) }
    bind:this={ root }
    { ...rest }
  >
    {#if hasTrack }
      <SplideTrack>
        <slot/>
      </SplideTrack>
    {:else }
      <slot/>
    {/if  }
  </div>
  