<template>
  <div class="container">
    <div class="form-row" :class="{ 'mb-on-warning': errors.length > 0 }">
      <div class="form-row-group" :class="{ 'mb-on-warning-mobile': errors.length > 0 }">
        <label for="fullName">ФИО<span>*</span></label>
        <input :class="{ 'warning': errors.length > 0 }" autocomplete="off" id="fullName" type="text" placeholder="Введите ФИО" name="fullName" v-model="fullName">
        <img v-if="fullName.length > 0" @click="resetFullName" class="form-row-group-reset" src="../assets/close.svg">
        <transition name="fade" v-on:enter="enter">
          <span v-if="errors.length > 0" class="form-row-group-warning"><img src="../assets/warning.svg">Необходимо ввести ФИО</span>
        </transition>
      </div>
      <div class="form-row-group">
        <label>Образование</label>
        <multiselect v-model="degreeStudyList"
                     :options="options"
                     tag-placeholder="Add this as new tag"
                     placeholder="Выберите образование"
                     :limit="3"
                     :max="3"
                     :searchable="false"
                     group-values="educationLevelList"
                     group-label="level"
                     :clear-on-select="false"
                     :group-select="false"
                     :preserve-search="true"
                     deselect-label=""
                     data-select=""
                     track-by="name"
                     :taggable="true"
                     :custom-label="customLabel"
                     :show-labels="false"
                     :multiple="true">
          <template slot="tag" slot-scope="props">
            <span class="multiselect__tag"><span>{{ props.option.label }}</span><i @click="removeSelected(props.option)" aria-hidden="true" class="multiselect__tag-icon"></i></span>
          </template>
        </multiselect>
      </div>
      <div class="form-row-group form-row-group-switch-form">
        <label class="form-row-group-switch">
          <input type="checkbox" v-model="isReadyMove">
          <span class="form-row-group-switch-slider"></span>
        </label>
        <span>Готов к переезду</span>
      </div>
    </div>
    <div class="form-row">
      <div class="form-row-tabs">
        <input type="radio" id="male" :class="{ 'checked': gender === 'male'}" name="gender" value="male" v-model="gender">
        <label class="male" for="male">Мужчина</label>
        <input type="radio" id="female" :class="{ 'checked': gender === 'female'}" name="gender" value="female" v-model="gender"/>
        <label class="female" for="female">Женщина</label>
      </div>
      <div class="form-row-checkbox">
        <input id="fullWorkDay" type="checkbox" v-model="isFullWorkDay">
        <label for="fullWorkDay">
          Полный рабочий день
        </label>
      </div>
      <div class="form-row-checkbox">
        <input type="checkbox" id="remoteWork" v-model="isRemoteWork">
        <label for="remoteWork">
          Удалённая работа
        </label>
      </div>
    </div>
    <div class="form-row buttons">
      <button :disabled='isDisabled' @click="reloadPage" class="form-row-cancel-button">Отменить</button>
      <button :disabled='isDisabled' @click="checkForm" class="form-row-save-button">Сохранить</button>
    </div>
    <transition name="fade" v-on:enter="enter">
      <div v-if="isFormCompletedSuccessfully" class="form-row success-message">
        <div class="success-message-items">
          <img src="../assets/success-checked.svg">
          <span>Данные успешно сохранены</span>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Multiselect from 'vue-multiselect'
export default {
  name: 'Form',
  components: {
    Multiselect
  },
  data() {
    return {
      fullName: [],
      isReadyMove: false,
      isFullWorkDay: false,
      isRemoteWork: false,
      gender: 'male',
      isFormCompletedSuccessfully: false,
      degreeStudyList: [],
      selected: [],
      errors: [],
      options: [
        {
          level: 'Среднее',
          educationLevelList: [
            { name: 'Среднее специальное', code: 'one', label: 'Среднее' },
            { name: 'Незаконченное высшее', code: 'two', label: 'Неполное высшее' },
          ]
        },
        {
          level: 'Высшее',
          educationLevelList: [
            { name: 'Магистр, кандидат, доктор наук', code: 'three', label: 'Высшее' }
          ]
        }
      ]
    };
  },
  methods: {
    resetFullName() {
      this.fullName = '';
    },
    removeSelected(item) {
     this.degreeStudyList = this.degreeStudyList.filter((value) => value.name !== item.name);
    },
    customLabel (option) {
      return option.name;
    },
    checkForm: function () {
      if (this.errors.length > 0) {
        return;
      } else {
        this.isFormCompletedSuccessfully = true;
        return;
      }
    },
    enter: function() {
      let that = this;
      setTimeout(function() {
        that.isFormCompletedSuccessfully = false;
      }, 3000); // hide the message after 3 seconds
    },
    reloadPage() {
      document.location.reload();
    }
  },
  computed: {
    isDisabled: function(){
      return !(this.fullName.length > 0 || this.degreeStudyList.length > 0 || this.isRemoteWork || this.isFullWorkDay || this.isReadyMove || this.gender === 'female');
    }
  },
  watch: {
    fullName: function (val) {
      if (val.length < 1) {
        this.errors.push('notFullName');
      } else {
        this.errors = [];
      }
    }
  }
}
</script>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
<style lang="css" scoped>
@import '../css/style.module.css';
</style>
