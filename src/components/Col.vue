<template>
  <td v-for="(name, i) in names"
      :key="i">
    <div :id="`${i}-${name}-${rowId}`"
         class="cell"
         :style="{'background-color': color}"></div>
  </td>
</template>
<script>
import names from './names';
import colors from './colors';

export default function Col({ rowId }) {
  const td = names.map((name, i) => {
    const random = Math.floor(Math.random() * colors.length - 1);
    const color = colors[random];
    return (
      <td key={i}>
        <div id={`${i}-${name}-${rowId}`} className="cell" style={{ 'background-color': color }}></div>
      </td>
    );
  });
  return td;
}
</script>

