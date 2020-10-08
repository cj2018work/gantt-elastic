<!--
/**
 * @fileoverview Event Ruler component
 * @license MIT
 * @author John Chen <cj.2018work@gmail.com>
 * @package GanttElastic
 */
-->

<template>
  <g
    class="gantt-elastic__chart-event-ruler-container"
    :style="{ ...root.style['chart-event-ruler-container'] }"
  >
    <line
      class="gantt-elastic__chart-event-ruler"
      :style="{ ...root.style['chart-row-event-ruler-axis'] }"
      :x1="0"
      :y1="task.y - 2"
      :x2="root.state.options.width + 'px'"
      :y2="task.y - 2"
    ></line>
    <circle
      v-for="event in eventsInTask"
      :key="event.key"
      :style="{ ...root.style['chart-row-event-ruler-point'], ...event.style.base, ...event.style[event.status] }"
      :cx="event.cx" 
      :cy="task.y - 2" 
      :r="event.r" 
      @click="emitEventRulerEvent('chart-event-ruler-point-click', $event, event)"
      @mouseenter="emitEventRulerEvent('chart-event-ruler-point-mouseenter', $event, event)"
      @mouseover="emitEventRulerEvent('chart-event-ruler-point-mouseover', $event, event)"
      @mouseout="emitEventRulerEvent('chart-event-ruler-point-mouseout', $event, event)"
    >
      <title>Status={{event.status}} - Progress={{event.progress}}%: {{event.memo}}</title>
    </circle>
  </g>
</template>

<script>
import dayjs from 'dayjs';

export default {
  name: 'EventRuler',
  inject: ['root'],
  data() {
    return {};
  },
  props: ['task'],
  computed: {
    /**
     * Get neccessary infos for each Event Ruler
     *
     * @returns {Array}
     */
    eventsInTask() {
      return (this.task.events || []).map(evt => {
        return {
          key: 'event-ruler-' + evt.time.getTime(),
          cx: this.root.timeToPixelOffsetX(evt.time.getTime()),
          time: evt.time,
          status: evt.status,
          progress: evt.progress,
          owner: evt.owner,
          memo: evt.memo,
          r: evt.r || 3,
          style: evt.style || {}
        }
      })
    }
  },
  methods: {
    emitEventRulerEvent(name, event, eventObj) {
      if (!this.root.state.options.scroll.scrolling) {
        this.root.$emit(name, {event, data: {event: eventObj, task: this.task}})
      }
    }
  }
};
</script>
