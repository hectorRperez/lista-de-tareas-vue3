<template>
	<div class="container">
		<div class="task__container">
			<div class="task__header">
				<h2>Task Board</h2>
				<p>Create a list of tasks</p>
				<form @submit.prevent="onClickSendTask">
					<!-- FIXME: EVITAR QUE LOS CAMPOS QUEDEN VACIOS -->
					<input type="text" placeholder="Add a new task" v-model="task.title" />
					<textarea placeholder="Add description" rows="5" v-model="task.description"></textarea>
					<button value="ADD">ADD</button>
				</form>
			</div>

			<div class="task__body">
				<ul>
					<li v-for="task in taskList" :key="task.id">
						{{ task.title }}
						<div class="task__button">
							<button class="button__done">DONE</button>
							<button class="button__delete" @click="showDialog(task.id)">DELETE</button>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>

	<Dialog :show="showDialogBox" :cancel="cancel" :confirm="confirm" title="Delete task?" description="Are you sure you want delete this task?" />
</template>

<script>
class ApiService {
	baseUrl = 'http://localhost:3000/api/task';

	async getAllTask() {
		const response = await fetch(this.baseUrl);
		return response.json();
	}

	async createTask(newTask) {
		const requestOption = {
			method: 'POST',

			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify(newTask),
		};
		const data = await fetch(this.baseUrl, requestOption);
		return data.json();
	}

	async deleteTask(taskId) {
		const requestOption = {
			method: 'DELETE',
			headers: { 'Content-Type': 'application/json' },
		};

		const response = await fetch(`${this.baseUrl}/${taskId}`, requestOption);
		return response.json();
	}
}

/**
 *
 * TODO: TODOS LOS COMPONENTES
 */
import Dialog from './DialogBox.vue';

export default {
	components: {
		Dialog,
	},

	data() {
		return {
			task: {},
			taskList: [],
			apiService: null,
			showDialogBox: false,
			idTask: null,
		};
	},

	async created() {
		this.apiService = new ApiService();
		this.taskList = await this.apiService.getAllTask();
	},

	methods: {
		onClickSendTask: async function () {
			this.apiService = new ApiService();

			await this.apiService.createTask(this.task);
			this.taskList = await this.apiService.getAllTask();

			this.task = {};
		},

		async onClickDeleteTask(taskId) {
			this.apiService = new ApiService();
			await this.apiService.deleteTask(taskId);

			console.log(taskId);
		},

		cancel() {
			console.log('cancel');
			this.showDialogBox = false;
		},

		async confirm() {
			this.apiService = new ApiService();
			await this.apiService.deleteTask(this.idTask);
			this.taskList = await this.apiService.getAllTask();

			this.showDialogBox = false;
		},

		showDialog(idTask) {
			this.idTask = idTask;
			this.showDialogBox = true;
		},
	},
};
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Fira+Sans:wght@100;200;300;400&display=swap');

:root {
	--bg-color: #272237;
	--green: #43dd69;
	--red: #d80046;
	--bg-color-light: #362c4f;
	--white: #fff;
}

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Fira Sans', sans-serif;
}

html {
	background: var(--bg-color);
}

.task__container {
	width: 450px;
	margin: auto;

	.task__header {
		max-width: 310px;
		margin: auto;

		h2 {
			font-size: 40px;
			color: var(--white);
		}
		p {
			color: var(--white);
		}

		form {
			display: flex;
			flex-direction: column;

			input {
				background: transparent;
				margin-top: 25px;
				border: none;
				border-bottom: 2px solid #716d80;
				margin-bottom: 1.5rem;
				outline: none;
				color: var(--white);
				font-size: 30px;
				padding: 10px;
			}

			textarea {
				background: transparent;
				color: var(--white);
				outline: none;
				border: none;
				font-size: 16px;
				padding: 10px;
			}

			button {
				background: var(--green);
				color: var(--bg-color);
				font-size: 18px;
				border-radius: 10px;
				padding: 10px;
				outline: none;
				border: none;
				font-weight: bold;
			}
		}
	}

	.task__body {
		margin-top: 15px;
		padding: 10px;

		ul {
			li {
				list-style: none;
				padding: 15px;
				margin: 10px 0;
				border-radius: 10px;

				display: flex;
				justify-content: space-between;
				background: var(--bg-color-light);
				color: var(--white);

				.button__done {
					background: var(--green);
					color: var(--bg-color);
					font-size: 14px;
					border-radius: 10px;
					padding: 5px;
					outline: none;
					border: none;
					font-weight: bold;
					margin-right: 8px;
				}
				.button__delete {
					background: var(--red);
					color: var(--white);
					font-size: 14px;
					border-radius: 10px;
					padding: 5px;
					outline: none;
					border: none;
					font-weight: bold;
				}
			}
		}
	}
}

.overlay {
	--tw-bg-opacity: 1;
	background-color: rgba(0, 0, 0, var(--tw-bg-opacity));
	--tw-bg-opacity: 0.5;
	height: 100%;
	position: fixed;
	top: 0px;
	right: 0px;
	bottom: 0px;
	left: 0px;
	width: 100%;
	z-index: 10;
}

.dialog {
	--tw-bg-opacity: 1;
	background-color: rgba(255, 255, 255, var(--tw-bg-opacity));
	border-radius: 0.75rem;
	margin-left: auto;
	margin-right: auto;
	margin-top: 2.5rem;
	max-width: 100%;
	width: 24rem;
}

.dialog__content {
	padding-left: 0.75rem;
	padding-right: 0.75rem;
	padding-top: 1rem;
	padding-bottom: 1rem;
}

.dialog__title {
	font-weight: 500;
	font-size: 1.125rem;
	line-height: 1.75rem;
	margin-bottom: 0.5rem;
	--tw-text-opacity: 1;
	color: rgba(17, 24, 39, var(--tw-text-opacity));
}

.dialog__description {
	font-size: 0.875rem;
	line-height: 1.25rem;
	margin-bottom: 1rem;
	--tw-text-opacity: 1;
	color: rgba(107, 114, 128, var(--tw-text-opacity));
}

.dialog__footer {
	display: flex;
	justify-content: flex-end;
	padding-top: 1rem;
	padding-bottom: 1rem;
}

.dialog__cancel {
	border-radius: 0.75rem;
	font-weight: 500;
	margin-right: 1rem;
}

.dialog__cancel:focus {
	outline: 2px solid transparent;
	outline-offset: 2px;
}

.dialog__cancel {
	padding-top: 0.75rem;
	padding-bottom: 0.75rem;
	padding-left: 1rem;
	padding-right: 1rem;
}

.dialog__cancel:focus {
	--tw-ring-offset-shadow: var(--tw-ring-inset) 0 0 0 var(--tw-ring-offset-width) var(--tw-ring-offset-color);
	--tw-ring-shadow: var(--tw-ring-inset) 0 0 0 calc(2px + var(--tw-ring-offset-width)) var(--tw-ring-color);
	box-shadow: var(--tw-ring-offset-shadow), var(--tw-ring-shadow), var(--tw-shadow, 0 0 #0000);
	--tw-ring-opacity: 1;
	--tw-ring-color: rgba(75, 85, 99, var(--tw-ring-opacity));
	--tw-ring-opacity: 0.5;
}

.dialog__cancel {
	--tw-text-opacity: 1;
	color: rgba(17, 24, 39, var(--tw-text-opacity));
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.dialog__cancel:hover {
	--tw-text-opacity: 1;
	color: rgba(55, 65, 81, var(--tw-text-opacity));
}

.dialog__confirm {
	--tw-bg-opacity: 1;
	background-color: rgba(254, 226, 226, var(--tw-bg-opacity));
	border-radius: 0.75rem;
	font-weight: 500;
	margin-right: 1rem;
}

.dialog__confirm:focus {
	outline: 2px solid transparent;
	outline-offset: 2px;
}

.dialog__confirm {
	padding-top: 0.75rem;
	padding-bottom: 0.75rem;
	padding-left: 1rem;
	padding-right: 1rem;
}

.dialog__confirm:focus {
	--tw-ring-offset-shadow: var(--tw-ring-inset) 0 0 0 var(--tw-ring-offset-width) var(--tw-ring-offset-color);
	--tw-ring-shadow: var(--tw-ring-inset) 0 0 0 calc(2px + var(--tw-ring-offset-width)) var(--tw-ring-color);
	box-shadow: var(--tw-ring-offset-shadow), var(--tw-ring-shadow), var(--tw-shadow, 0 0 #0000);
	--tw-ring-opacity: 1;
	--tw-ring-color: rgba(220, 38, 38, var(--tw-ring-opacity));
	--tw-ring-opacity: 0.5;
}

.dialog__confirm {
	--tw-text-opacity: 1;
	color: rgba(220, 38, 38, var(--tw-text-opacity));
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

.dialog__confirm:hover {
	--tw-bg-opacity: 1;
	background-color: rgba(252, 165, 165, var(--tw-bg-opacity));
	--tw-text-opacity: 1;
	color: rgba(153, 27, 27, var(--tw-text-opacity));
}
</style>
