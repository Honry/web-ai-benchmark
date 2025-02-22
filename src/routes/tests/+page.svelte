<script>
	import Header from '$lib/components/Header.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import Environment from '$lib/components/Environment.svelte';
	import Info from '$lib/components/Info.svelte';
	import Clock from '$lib/components/svg/Clock.svelte';
	import { models } from '$lib/config';
	import { beforeUpdate, onMount, onDestroy } from 'svelte';
	import OnnxFull from '$lib/components/svg/OnnxFull.svelte';
	import TfliteFull from '$lib/components/svg/TfliteFull.svelte';
	import Onnx from '$lib/components/svg/Onnx.svelte';
	import Tflite from '$lib/components/svg/Tflite.svelte';
	import { base } from '$app/paths';
	import { autoStore } from '$lib/store/store';
	import {
		resetStore,
		getModelNameById,
		getModelDataTypeById,
		getModelDescriptionById,
		getModelNoteById,
		getModelTypeById,
		sortModelById,
		getModelSizeById,
		getModelInt8Count
	} from '$lib/assets/js/utils';

	/**
	 * @type {string[]}
	 */
	$: uniqueModels = [];
	$: int8Count = 0;
	let subModels = models;
	let selected = 'onnx';

	const typeChange = (/** @type {{ currentTarget: { value: string; }; }} */ event) => {
		selected = event.currentTarget.value;
		console.log(selected);
		subModels = models.filter((item) => item.format === selected);
		console.log(subModels);
		subModels = sortModelById(subModels);
		console.log(subModels);
		uniqueModels = [...new Set(subModels.map((model) => model.id))];
		console.log(uniqueModels);
		uniqueModels = uniqueModels;
	};

	beforeUpdate(() => {});

	onMount(() => {
		resetStore();
		autoStore.update(() => false);
		subModels = models.filter((item) => item.format === 'onnx');
		uniqueModels = sortModelById(subModels);
		uniqueModels = [...new Set(uniqueModels.map((model) => model.id))];
		uniqueModels = uniqueModels;
		int8Count = getModelInt8Count(models);
	});

	onDestroy(() => {});
</script>

<Header />

<div class="tqtitle">
	<div class="title tq">Benchmark Tests</div>
</div>

<div class="modelselection">
	<div class="tabs">
		<input
			type="radio"
			on:change={typeChange}
			checked={selected === 'onnx'}
			id="r-onnx"
			value="onnx"
			name="tabs"
		/>
		<label class="tab" for="r-onnx"><OnnxFull /></label>
		<input
			type="radio"
			on:change={typeChange}
			checked={selected === 'tflite'}
			id="r-tflite"
			value="tflite"
			name="tabs"
		/>
		<label class="tab" for="r-tflite"><TfliteFull /></label>
		<span class="glider"></span>
	</div>
</div>

<div>
	<div class="title tq">FLOAT32</div>
	<div class="tq benchmark">
		{#each uniqueModels as model}
			{#if model !== 'model_access_check'}
				{#if getModelDataTypeById(model) === 'fp32'}
					<div
						class="q tests {model}"
						title="{model.replaceAll('_', '-')} · {getModelDescriptionById(
							model
						)} · {getModelNoteById(model)}"
					>
						<div class="status_1 s">
							<Clock />
						</div>
						{#if getModelTypeById(model) === 'onnx'}
							<div class="onnx">
								<Onnx />
							</div>
						{/if}

						{#if getModelTypeById(model) === 'tflite'}
							<div class="tflite">
								<Tflite />
							</div>
						{/if}

						{#if model.indexOf('_merged') > -1}
							<span class="kvcache">KV-C</span>
						{/if}

						{#if model.indexOf('_with_past') > -1}
							<span class="kvcache">PAST</span>
						{/if}

						<a href="{base}/run/{model}" class=""
							>{getModelNameById(model)} ·
							{#if getModelSizeById(model)}{getModelSizeById(model)}{/if}</a
						>
					</div>
				{/if}
			{/if}
		{/each}
	</div>

	<div class="title tq fp16">FLOAT16</div>
	<div class="tq benchmark">
		{#each uniqueModels as model}
			{#if model !== 'model_access_check'}
				{#if getModelDataTypeById(model) === 'fp16'}
					<div
						class="q tests {model}"
						title="{model.replaceAll('_', '-')} · {getModelDescriptionById(
							model
						)} · {getModelNoteById(model)}"
					>
						<div class="status_1 s">
							<Clock />
						</div>
						{#if getModelTypeById(model) === 'onnx'}
							<div class="onnx">
								<Onnx />
							</div>
						{/if}

						{#if getModelTypeById(model) === 'tflite'}
							<div class="tflite">
								<Tflite />
							</div>
						{/if}

						{#if model.indexOf('_merged') > -1}
							<span class="kvcache">KV-C</span>
						{/if}

						<a href="{base}/run/{model}" class=""
							>{getModelNameById(model)} ·
							{#if getModelSizeById(model)}{getModelSizeById(model)}{/if}</a
						>
					</div>
				{/if}
			{/if}
		{/each}
	</div>

	{#if int8Count > 0}
		<div class="title tq int8">INT8</div>
		<div class="tq benchmark">
			{#each uniqueModels as model}
				{#if model !== 'model_access_check'}
					{#if getModelDataTypeById(model) === 'int8'}
						<div
							class="q tests {model}"
							title="{model.replaceAll('_', '-')} · {getModelDescriptionById(
								model
							)} · {getModelNoteById(model)}"
						>
							<div class="status_1 s">
								<Clock />
							</div>
							{#if getModelTypeById(model) === 'onnx'}
								<div class="onnx">
									<Onnx />
								</div>
							{/if}

							{#if getModelTypeById(model) === 'tflite'}
								<div class="tflite">
									<Tflite />
								</div>
							{/if}

							{#if model.indexOf('_merged') > -1}
								<span class="kvcache">KV-C</span>
							{/if}

							<a href="{base}/run/{model}" class=""
								>{getModelNameById(model)} ·
								{#if getModelSizeById(model)}{getModelSizeById(model)}{/if}</a
							>
						</div>
					{/if}
				{/if}
			{/each}
		</div>
	{/if}

	<Environment />
	<Info />
</div>
<Footer />

<style>
	.title {
		text-align: center;
		color: var(--red);
	}

	.tq {
		margin-bottom: 10px;
	}

	.tq .q.tests {
		display: flex;
		align-items: center;
	}

	.tq .q.tests a {
		margin-left: 20px;
	}

	.tq .q.tests a:hover {
		color: var(--orange);
	}

	.modelselection {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.tabs {
		display: flex;
		background-color: #fff;
		box-shadow:
			0 0 1px 0 rgba(var(--red), 0.15),
			0 6px 12px 0 rgba(var(--red), 0.15);
		padding: 6px 20px;
		border-radius: 99px;
		margin: 0 auto;
		text-align: center;
	}

	input[type='radio'] {
		display: none;
	}

	.tab {
		display: flex;
		align-items: center;
		justify-content: center;
		padding: 6px 20px;
		font-weight: 500;
		border-radius: 99px;
		cursor: pointer;
		transition: color 0.15s ease-in;
	}

	input[type='radio'] {
		&:checked {
			& + label {
				color: var(--red);
			}
		}
	}

	input[id='r-onnx'] {
		&:checked {
			& ~ .glider {
				transform: translateX(0);
			}
		}
	}

	input[id='r-tflite'] {
		&:checked {
			& ~ .glider {
				transform: translateX(100%);
			}
		}
	}
	/* 
	input[id='radio-3'] {
		&:checked {
			& ~ .glider {
				transform: translateX(200%);
			}
		}
	} */

	.glider {
		position: absolute;
		display: flex;
		height: 30px;
		width: 140px;
		background-color: var(--red-005);
		z-index: 1;
		border-radius: 99px;
		transition: 0.25s ease-out;
	}

	.benchmark.tq .onnx,
	.benchmark.tq .tflite {
		margin: 0 0 2px 6px;
	}

	@media (max-width: 700px) {
		.tabs {
			transform: scale(0.6);
		}
	}

	.mobilenet_v2,
	.mobilenet_v3,
	.efficientnet_lite {
		background-color: var(--red-005);
	}

	.mobilenet_v2_fp16,
	.efficientnet_lite_fp16,
	.resnet50_v1_fp16,
	.squeezenet_fp16 {
		background-color: var(--fp16-005);
	}

	.mobilenet_v2_12_int8 {
		background-color: var(--p-005);
	}

	.kvcache {
		display: inline-block;
		margin-left: 8px;
		font-size: 0.7rem;
		padding: 0px 2px;
		border: 1px solid var(--grey-02);
	}
</style>
