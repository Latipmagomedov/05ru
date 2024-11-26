<script setup>
import { onMounted, ref } from 'vue'
import ControlButtons from '@/components/TextEditor/ControlButtons.vue'

const editor = ref('')

const editorField = ref()
const editorFieldHTML = ref()

// объект истории действий
const history = {
  back: [],
  forward: [],
}

// Сохранение действий
const performAction = () => {
  if (
    !history.back.length ||
    history.back[history.back.length - 1] !== editorFieldHTML.value.innerHTML
  ) {
    history.back.push(editorFieldHTML.value.innerHTML)
  }
}

//Возврат действий
const undo = () => {
  if (!history.back.length) return
  history.forward.push(editorFieldHTML.value.innerHTML)
  editor.value = history?.back.pop()
  editorFieldHTML.value.innerHTML = editor.value
}

// Повтор действий
const redo = () => {
  if (!history.forward.length) return
  history.back.push(editorFieldHTML.value.innerHTML)
  editor.value = history?.forward.pop()
  editorFieldHTML.value.innerHTML = editor.value
}

// Вызывается при каждом изменении в редакторе и сохраняет действия
const changeEditor = () => {
  history.forward = []
  history.back.push(editorFieldHTML.value.innerHTML)
}

// Для обертывания выделенного текста в тег
const wrapTag = (tagName, style) => {
  const tag = document.createElement(tagName)
  if (style) tag.style.cssText = style.join('')
  const userSelection = window.getSelection()
  if (!userSelection.anchorNode || !userSelection.getRangeAt(0).toString()) {
    alert('Выделите нужный для форматирования текст и нажмите на нужный тип форматирования')
    return
  }
  const selectedTextRange = userSelection.getRangeAt(0)
  selectedTextRange.surroundContents(tag)

  if (tagName === 'p') tag.innerText = `\n${tag.innerText}\n\n`
}

// Форматирование текста
const formatText = (type) => {
  performAction()
  if (type === 'title') wrapTag('h3', ['font-size: 40px;'])
  if (type === 'paragraph') wrapTag('p')
}

// Добавление изображения
const addImage = () => {
  const link = prompt('Вставьте ссылку на изображение')
  if (!link) return

  const selection = document.getSelection()
  if (
    document.execCommand &&
    document.queryCommandSupported('insertImage') &&
    selection.anchorNode
  ) {
    document.execCommand('insertImage', false, link)
  } else {
    editor.value += `<img src="${link}" alt="image" style="max-width: 400px">`
  }
}

// Копирование HTML
const copy = () => navigator.clipboard.writeText(editorFieldHTML.value.innerHTML)

onMounted(() => {
  editorFieldHTML.value.focus()
  editorFieldHTML.value.innerHTML = editor.value
})
</script>

<template>
  <div class="editor" ref="editorField">
    <ControlButtons
      @undo="undo"
      @redo="redo"
      @formatText="formatText"
      @addImage="addImage"
      @copy="copy"
    />

    <div contenteditable @input="changeEditor" ref="editorFieldHTML" class="editor__field"></div>
  </div>
</template>

<style scoped lang="scss">
.editor {
  &__field {
    min-height: 600px;
    margin-top: 31px;
    padding-bottom: 60px;
    display: flex;
    flex-direction: column;
    font-size: 15px;
    outline: none;

    &::v-deep(img) {
      max-width: 400px;
    }
  }
}
</style>
