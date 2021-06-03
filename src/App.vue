<template>
	<main class="container py-4">
		<h1>Laravel model docblocker</h1>

		<div class="mt-3 row">
			<div class="col-md-6">
				<span class="h5">Migration</span>
				<textarea class="form-control mb-3" v-model="migration" @change="update"></textarea>
				<input type="text" class="form-control" v-model="description" @change="update" placeholder="Description">
			</div>
			<div class="col-md-6">
				<span class="h5">Docblock</span>
				<textarea class="form-control mb-3" readonly v-model="docblock"></textarea>
				<button class="btn btn-secondary w-100" @click="copy">Copy</button>
			</div>
		</div>
	</main>
</template>

<script>
/* eslint-disable no-useless-escape */

const types = {
	'id': 'int',
	'rememberToken': 'string',
	'foreign': 'int',
	'increments': 'int',
	'integerIncrements': 'int',
	'tinyIncrements': 'int',
	'smallIncrements': 'int',
	'mediumIncrements': 'int',
	'bigIncrements': 'int',
	'char': 'string',
	'string': 'string',
	'tinyText': 'string',
	'text': 'string',
	'mediumText': 'string',
	'longText': 'string',
	'integer': 'int',
	'tinyInteger': 'int',
	'smallInteger': 'int',
	'mediumInteger': 'int',
	'bigInteger': 'int',
	'unsignedInteger': 'int',
	'unsignedTinyInteger': 'int',
	'unsignedSmallInteger': 'int',
	'unsignedMediumInteger': 'int',
	'unisgnedBigInteger': 'int',
	'foreignId': 'int',
	'foreignIdFor': 'int',
	'float': 'float',
	'double': 'double',
	'decimal': 'float',
	'unsignedFloat': 'float',
	'unsignedDouble': 'float',
	'unsignedDecimal': 'float',
	'boolean': 'bool',
	'enum': 'string',
	'set': 'string',
	'json': 'string',
	'josnb': 'string',
	'date': '\\Carbon\\Carbon',
	'dateTime': '\\Carbon\\Carbon',
	'dateTimeTz': '\\Carbon\\Carbon',
	'time': '\\Carbon\\Carbon',
	'timeTz': '\\Carbon\\Carbon',
	'timestamp': '\\Carbon\\Carbon',
	'timestampTz': '\\Carbon\\Carbon',
	'year': '\\Carbon\\Carbon',
	'binary': 'string',
	'uuid': 'string',
	'foreignUuid': 'string',
	'ipAddress': 'string',
	'macAddress': 'string',
};

const aliases = {
	'id': 'id',
	'rememberToken': 'remember_token?',
};

export default {
	name: 'App',
	data: function () {
		return {
			migration: null,
			description: null,
			docblock: null,
		};
	},
	methods: {
		update() {
			var cols = this.migration.trim().split('\n');
			cols = cols.map(col => {
				col = col.trim();
				let call = col.split('->')[1].replace(';', '');

				let method = call.split('(')[0];

				if (method.includes('timestamps'))
					return ' *\n * @property \\Carbon\\Carbon $created_at?\n * @property \\Carbon\\Carbon $updated_at?';
				else if (method.includes('softDeletes'))
					return ' * @property \\Carbon\\Carbon $deleted_at?';

				let args = call.match(/\(([^)]+)\)/);
				let name = (args ? args[0].replaceAll(/[\(\)']/g, '') : aliases[method]);

				return ` * @property ${types[method] ?? 'UNKNOWN'} $${name}` + (col.includes('nullable()') ? '?' : '');
			});
			this.docblock = '/**\n' + (this.description ? ' * ' + this.description + '\n *\n' : '') + cols.join('\n') + '\n */';
		},
		copy() {
			navigator.clipboard.writeText(this.docblock);
		},
	},
};
</script>

<style>
textarea {
	width: 100%;
	min-height: 70vh !important;
}
</style>
