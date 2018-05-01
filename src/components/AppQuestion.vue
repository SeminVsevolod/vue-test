<template>
	<div class="form-group">
  <h3>{{ quest.title }}</h3>
  <hr>
  <div class="form-check" v-for="value in quest.answers">
  	<label class="form-check-label">
  	 <input class="form-check-input" :type="quest.type" v-bind:value="value" v-model="userAnswer" @change="onChange">
  	 {{ value }}
  	</label>
  </div>
  <!-- {{ quest.answers }} -->
  <hr>
  <button class="btn btn-success" :disabled="!activated" @click.prevent="onClick">
			Дальше
		</button>
		<!-- {{ userAnswer }} -->
 </div>
</template>

<script>
	export default {
		props: ['quest'], //принимает объект answers из массива quests
		data(){
			return {
				activated: false, //активна ли кнопка "Дальше"
				userAnswer: [] //ответ на текущий вопрос
			}
		},
		methods: {
			onClick(){
				// console.log(this);
				this.$emit('answered', this.userAnswer);
				this.activated = false;
				this.userAnswer = [];
			},
			onChange(){ //при выборе 
				if (this.userAnswer.length == 0) {
				 this.activated = false
				} else { 
					this.activated = true
				}
    // console.log(this);
			}
		}
	}
</script>