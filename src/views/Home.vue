<template>
	<v-container width="50vw">
		<v-col cols="12">
			<div class="text-h4 mb-5 text-center">
				Hvatanje podataka i vizualizacija u tablici
			</div>
			<v-alert v-if="visibleAlert" :type="type">{{ message }}</v-alert>
			<v-form @submit.prevent="handleSubmit">
				<v-text-field
					v-model="name"
					outlined
					hint="Upišite neko ime"
					label="Unos Imena"
				></v-text-field>
				<v-btn color="success" class="mb-5" @click.prevent="handleSubmit"
					>Submit</v-btn
				>
			</v-form>
			<v-data-table
				style="
					background-color: #b8c6db !important;
					background-image: linear-gradient(
						315deg,
						#b8c6db 0%,
						#f5f7fa 74%
					) !important;
				"
				:headers="headers"
				:items="items"
			>
			</v-data-table>
		</v-col>
	</v-container>
</template>

<script>
import axios from "axios";

export default {
	name: "Home",
	data() {
		return {
			name: "",
			headers: [
				{
					text: "Ime",
					align: "start",
					sortable: false,
					value: "ime",
				},
				{
					text: "Država",
					align: "start",
					sortable: false,
					value: "država",
				},
				{
					text: "Godine",
					align: "start",
					sortable: true,
					value: "godine",
				},
				{
					text: "Spol",
					align: "start",
					sortable: false,
					value: "spol",
				},
			],
			items: [],
			visibleAlert: false,
			message: "",
			type: "",
		};
	},
	methods: {
		async handleSubmit() {
			if (!this.name || this.name == "") {
				return this.sendAlert("error", "Polje za ime ne može ostati prazno 😜");
			}
			const res = await axios.get(`https://api.agify.io?name=${this.name}`);
			const resData = res.data;

			const res2 = await axios.get(
				`https://api.genderize.io?name=${this.name}`
			);
			const resData2 = res2.data;

			const res3 = await axios.get(
				`https://api.nationalize.io?name=${this.name}`
			);

			const resData3 = res3.data;

			if (resData) {
				const itemTemplate = {
					ime: this.name,
					država: this.joinStringFromArr(resData3.country) || "not found",
					godine: resData.age || "not found",
					spol: `${resData2.gender || "not found"} (${
						(resData2.probability * 100).toFixed(2) || 0
					})`,
				};
				this.items.push(itemTemplate);
				this.name = "";
			}
			if (!resData.age || !resData2.gender || !resData3.country) {
				this.sendAlert(
					"warning",
					"Podatci za uneseno ime su dohvaćeni djelomično ili je rezultat prazan."
				);
				// av
				return;
			}
			this.sendAlert("success", "Uspješno uhvaćeni podaci");
		},
		joinStringFromArr(arr) {
			let newStr = "";
			arr.forEach((x, index) => {
				if (arr[index + 1]) {
					newStr += `${x.country_id} (${(x.probability * 100).toFixed(2)}%), `;
				} else {
					newStr += `${x.country_id} (${(x.probability * 100).toFixed(2)}%) `;
				}
			});
			return newStr;
		},
		sendAlert(type, message, duration = 5000) {
			this.type = type;
			this.message = message;
			this.visibleAlert = true;
			setTimeout(() => {
				this.type = "";
				this.message = "";
				this.visibleAlert = false;
			}, duration);
		},
	},
};
</script>
