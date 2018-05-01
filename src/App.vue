<template>
	<div class="wrapper">
  <div class="sample">
  	<div class="progress" v-if="!testPassed">
					<div class="progress-bar" :style="progressWidth"></div>
				</div>
			<!-- Показать форму, если тест ещё не пройден	 -->
   <form @submit.prevent="testPassed" v-if="!testPassed">
				<div>
					<h2>Вопрос {{ done + 1 }} из {{ quests.length }}:</h2>
	    <app-question :quest="quests[done]" @answered="onAnswered($event)"></app-question>
				</div>
			</form>
			<!-- Иначае показать результат	 -->
			<template v-else>
				<h2>Ваш результат: {{ result }} из {{ quests.length }}</h2>
				<hr>
				<table class="table table-bordered table-striped">
					<thead>
						<tr>
							<th>Вопрос</th><th>Ваш ответ</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(item, index) in quests">
							<td>{{ item['title'] }}</td><td><span class="fa" :class="validClass(isValidAnswers[index])"></span> {{ userAnswers[index] }}</td>
						</tr>
					</tbody>
				</table>
			</template>
		</div>
    </div>
</template>

<script>
	import AppQuestion from './components/AppQuestion'

	export default {
		data(){
			return {
				done: 0, //кол-во пройденных вопросов
				result: 0, //кол-во правильных ответов
				testPassed: false, //тест пройден?
				quests: getQuests(), //получить массив вопросов
				userAnswers: [], //ответы пользователя
				isValidAnswers: [] //boolean массив (верно ли ответил пользователь)
			}
		},
		methods: {
			onAnswered(answer){
				this.userAnswers.push(answer);
				if (this.done == (this.quests.length - 1)) { 
					this.testPassed = true;
				}
				//если тип вопроса checkbox, то ответ - массив -> проверяем массив на равенство
				if (answer instanceof Array) {
					if (this.quests[this.done].rightAns.hasAll(answer)) { //проверка на равенство двух массивов
						this.result++;
						this.isValidAnswers.push(true) //Верно
					} else {
						this.isValidAnswers.push(false) //Неверно
					}
				//иначе тип вопроса radio, и ответ - строка -> проверяем их на равенство
				} else if (this.quests[this.done].rightAns == answer) {
					this.result++;
					this.isValidAnswers.push(true) //Верно
				} else {
					this.isValidAnswers.push(false) //Неверно
				}
				//Вопрос пройден
				this.done++;
			},
			validClass(isValid){
				return isValid ? 
						'fa-check-circle text-success' :
						'fa-exclamation-circle text-danger';
			}
		},
		computed: {
			progressWidth(){
				return {
					width: (this.done / this.quests.length * 100) + '%'
				}
			}
		},
		components: {
			AppQuestion
		}
	}

	//проверка на равенство двух массивов
	Array.prototype.hasAll = function(a) {
		let hash = this.reduce(function(acc, i) { acc[i] = true; return acc; }, {});
		return a.every(function(i) { return i in hash; });
 };

	//Возвращяет массив вопросов, каждый вопрос - объект, в котором ответы хранятся в массиве :-)
	function getQuests(){
		return [
			{
				type: 'radio',
				title: 'Какой тег задает ссылку?',
				answers: [
					'a',
					'div',
					'span',
					'img'
				],
				rightAns: 'a'
			},
			{
				type: 'checkbox',
				title: 'Какие из этих тегов строчные?',
				answers: [
					'a',
					'div',
					'span',
					'img'
				],
				rightAns: [
				 'a',
				 'span'
				 ]
			},
			{
				type: 'radio',
				title: 'Какого тега НЕ существует в спецификации HTML5?',
				answers: [
					'figure',
					'h7',
					'legend',
					'details'
				],
				rightAns: 'h7'
			},
			{
				type: 'checkbox',
				title: 'Укажите все ОБЯЗАТЕЛЬНЫЕ атрибуты тега <img>:',
				answers: [
					'sizes',
					'alt',
					'src',
					'srcset'
				],
				rightAns: [
				 'alt',
				 'src'
				 ]
			},
			{
				type: 'radio',
				title: 'Какое CSS-правило из приведенных ниже имеет наибольший приоритет?',
				answers: [
					'#floor span',
					'.content #my-cat span',
					'#floor .cat-in-box',
					'.content .cat-in-box span'
				],
				rightAns: '.content #my-cat span'
			},
			{
				type: 'checkbox',
				title: 'Укажите все универсальные атрибуты тегов из приведенных ниже:',
				answers: [
					'contextmenu',
					'data-*',
					'src',
					'itemprop'
				],
				rightAns: [
				 'contextmenu',
				 'data-*',
				 'itemprop'
				 ]
			},
			{
				type: 'radio',
				title: 'Для какого элемента dipslay: inline установлен по умолчанию?',
				answers: [
					'<span>',
					'<p>',
					'<h1>',
					'<div>'
				],
				rightAns: '<span>'
			},
			{
				type: 'radio',
				title: 'Какое свойство не работает для блочных элементов?',
				answers: [
					'height',
					'width',
					'margin',
					'vertical-align'
				],
				rightAns: 'vertical-align'
			},
			{
				type: 'radio',
				title: 'Чему равна высота строчно-блочного элемента при наличии height?',
				answers: [
					'высота содержимого плюс margin, border и padding',
					'height плюс border и padding',
					'height плюс margin, border и padding',
					'высота содержимого плюс border и padding'
				],
				rightAns: 'height плюс margin, border и padding'
			},
			{
				type: 'radio',
				title: 'Какая будет ширина блока со следующим стилем: width: 200px; padding: 10px; border 2px solid black;',
				answers: [
					'200px',
					'224px',
					'220px',
					'212px'
				],
				rightAns: '224px'
			}
		];
	};
</script>

<style scoped>
	.wrapper{
		max-width: 600px;
		margin: 20px auto;
	}
</style>