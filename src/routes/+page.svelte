<script lang="ts">
	import { onMount, tick } from 'svelte';

	const WIDTH = 3000;
	const HEIGHT = 3400;

	let BACKGROUND;
	let BLOCK_ALL_CHZZK;
	let BLOCK_ALL_YOUTUBE;
	let BLOCK_DAWN_CHZZK;
	let BLOCK_DAWN_YOUTUBE;
	let BLOCK_DOJIN_CHZZK;
	let BLOCK_DOJIN_YOUTUBE;
	let BLOCK_KYEONGYOONG_CHZZK;
	let BLOCK_KYEONGYOONG_YOUTUBE;
	let BLOCK_REKURIM_CHZZK;
	let BLOCK_REKURIM_YOUTUBE;
	let PAW_BLUE;
	let PAW_MINT;
	let PAW_PURPLE;
	let PAW_YELLOW;
	let PAW_GREY;
	let ANNOUNCEMENT;

	const ALL_COLOR = '#A94E77';
	const DAWN_COLOR = '#3B4DA1';
	const DOJIN_COLOR = '#5847AE';
	const REKURIM_COLOR = '#1B858D';
	const KYEONGYOONG_COLOR = '#9A6718';

	function loadImage(src: string): HTMLImageElement {
		const image = new Image();
		image.src = src;
		return image;
	}

	function padZero(num: number, size: number): string {
		let s = num.toString();
		while (s.length < size) s = '0' + s;
		return s;
	}

	let mondayPlans = $state([]);
	let tuesdayPlans = $state([]);
	let wednesdayPlans = $state([]);
	let thursdayPlans = $state([]);
	let fridayPlans = $state([]);
	let saturdayPlans = $state([]);
	let sundayPlans = $state([]);

	const MONTH_NAMES = [
		'JAN',
		'FEB',
		'MAR',
		'APR',
		'MAY',
		'JUN',
		'JUL',
		'AUG',
		'SEP',
		'OCT',
		'NOV',
		'DEC'
	];

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	let fromDate: string = new Date().toISOString().split('T')[0];
	let yellowPaw = $state(false);
	let mintPaw = $state(false);
	let bluePaw = $state(false);
	let purplePaw = $state(false);

	let notice = $state('');

	function downloadCanvasAsImage() {
		const link = document.createElement('a');
		link.href = canvas.toDataURL('image/png');
		link.download = 'chzzk_block.png';
		link.click();
	}

	function draw() {
		fromDate = new Date(fromDate);

		// draw background
		ctx.drawImage(BACKGROUND, 0, 0, WIDTH, HEIGHT);

		// draw date
		ctx.fillStyle = 'white';
		ctx.font = '87.5px JZukinie';
		ctx.textAlign = 'center';
		ctx.textBaseline = 'middle';
		ctx.letterSpacing = '-2px';
		ctx.fillText(padZero(fromDate.getDate(), 2), 318.8, 409.3);
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 6 * 24 * 60 * 60 * 1000).getDate(), 2),
			684.3,
			409.3
		);

		ctx.fillStyle = '#6882CC';
		ctx.font = '37.5px JZukinie';
		ctx.textAlign = 'center';
		ctx.textBaseline = 'middle';
		ctx.letterSpacing = '-2px';
		ctx.fillText(MONTH_NAMES[fromDate.getMonth()], 320.6, 299.3);
		ctx.fillText(
			MONTH_NAMES[new Date(fromDate.getTime() + 6 * 24 * 60 * 60 * 1000).getMonth()],
			685.6,
			299.3
		);

		ctx.fillStyle = '#174C99';
		ctx.font = '100px JZukinie';
		ctx.textAlign = 'center';
		ctx.textBaseline = 'middle';
		ctx.letterSpacing = '-2px';
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 0 * 24 * 60 * 60 * 1000).getDate(), 2),
			354.5,
			906.2
		);
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 1 * 24 * 60 * 60 * 1000).getDate(), 2),
			736.6,
			906.2
		);
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 2 * 24 * 60 * 60 * 1000).getDate(), 2),
			1118.7,
			906.2
		);
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 3 * 24 * 60 * 60 * 1000).getDate(), 2),
			1500.8,
			906.2
		);
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 4 * 24 * 60 * 60 * 1000).getDate(), 2),
			1882.9,
			906.2
		);

		ctx.fillStyle = '#0673A6';
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 5 * 24 * 60 * 60 * 1000).getDate(), 2),
			2265.0,
			906.2
		);

		ctx.fillStyle = '#C1384B';
		ctx.fillText(
			padZero(new Date(fromDate.getTime() + 6 * 24 * 60 * 60 * 1000).getDate(), 2),
			2647.2,
			906.2
		);

		// draw paws
		ctx.drawImage(yellowPaw ? PAW_YELLOW : PAW_GREY, 2237, 296);
		ctx.drawImage(mintPaw ? PAW_MINT : PAW_GREY, 2373, 296);
		ctx.drawImage(bluePaw ? PAW_BLUE : PAW_GREY, 2509, 296);
		ctx.drawImage(purplePaw ? PAW_PURPLE : PAW_GREY, 2645, 296);

		// draw blocks
		for (let i = 0; i < 7; i++) {
			const plans = [
				mondayPlans,
				tuesdayPlans,
				wednesdayPlans,
				thursdayPlans,
				fridayPlans,
				saturdayPlans,
				sundayPlans
			][i];
			for (const plan of plans) {
				if (!plan.who || !plan.platform) continue;

				let blockImage;
				if (plan.who === '하울링크') {
					blockImage = plan.platform === '유튜브' ? BLOCK_ALL_YOUTUBE : BLOCK_ALL_CHZZK;
				} else if (plan.who === '이다음') {
					blockImage = plan.platform === '유튜브' ? BLOCK_DAWN_YOUTUBE : BLOCK_DAWN_CHZZK;
				} else if (plan.who === '연도진') {
					blockImage = plan.platform === '유튜브' ? BLOCK_DOJIN_YOUTUBE : BLOCK_DOJIN_CHZZK;
				} else if (plan.who === '리쿠림') {
					blockImage = plan.platform === '유튜브' ? BLOCK_REKURIM_YOUTUBE : BLOCK_REKURIM_CHZZK;
				} else if (plan.who === '경융') {
					blockImage =
						plan.platform === '유튜브' ? BLOCK_KYEONGYOONG_YOUTUBE : BLOCK_KYEONGYOONG_CHZZK;
				}

				const width = blockImage.width;
				const height = blockImage.height;
				const x = 176 + (i * (2470 - 176)) / 6; // 요일에 따른 x 좌표
				const index = plans.indexOf(plan);
				const y = 1109 + index * (1482 - 1109); // 일정 개수에 따른 y 좌표 (임시로 i 사용, 실제로는 plan의 index를 사용해야 함)
				ctx.drawImage(blockImage, x, y);

				ctx.fillStyle = [ALL_COLOR, DAWN_COLOR, DOJIN_COLOR, REKURIM_COLOR, KYEONGYOONG_COLOR][
					['하울링크', '이다음', '연도진', '리쿠림', '경융'].indexOf(plan.who)
				];
				ctx.font = '32.5px LaundryGothic';
				ctx.textAlign = 'center';
				ctx.textBaseline = 'middle';
				ctx.letterSpacing = '-1px';
				ctx.fillText(plan.who, x + width / 2, y + 52.7);

				ctx.strokeStyle = ctx.fillStyle;
				ctx.fillStyle = 'white';
				ctx.font = '57px LaundryGothic';
				ctx.textAlign = 'center';
				ctx.textBaseline = 'middle';
				ctx.lineWidth = 8;
				ctx.strokeText(plan.title, x + width / 2, y + 152);
				ctx.fillText(plan.title, x + width / 2, y + 152);

				ctx.fillStyle = ctx.strokeStyle;
				ctx.font = '32.5px LaundryGothic';
				ctx.textAlign = 'center';
				ctx.textBaseline = 'middle';
				ctx.fillText(plan.subtitle, x + width / 2, y + 211.3);

				ctx.fillStyle = 'white';
				ctx.font = '32.5px LaundryGothic';
				ctx.textAlign = 'center';
				ctx.textBaseline = 'middle';
				ctx.fillText(plan.time, x + width / 2, y + 285.5);
			}
		}

		// notice
		if (notice) {
			ctx.drawImage(ANNOUNCEMENT, 176, 3019);
			ctx.fillStyle = '#3367B4';
			ctx.font = '54.2px LaundryGothic';
			ctx.textAlign = 'left';
			ctx.textBaseline = 'middle';
			ctx.fillText(notice, 327.4, 3102);
		}
	}

	onMount(async () => {
		BACKGROUND = loadImage('/background.png');
		BLOCK_ALL_CHZZK = loadImage('/block_all_chzzk.png');
		BLOCK_ALL_YOUTUBE = loadImage('/block_all_youtube.png');
		BLOCK_DAWN_CHZZK = loadImage('/block_dawn_chzzk.png');
		BLOCK_DAWN_YOUTUBE = loadImage('/block_dawn_youtube.png');
		BLOCK_DOJIN_CHZZK = loadImage('/block_dojin_chzzk.png');
		BLOCK_DOJIN_YOUTUBE = loadImage('/block_dojin_youtube.png');
		BLOCK_KYEONGYOONG_CHZZK = loadImage('/block_kyeongyoong_chzzk.png');
		BLOCK_KYEONGYOONG_YOUTUBE = loadImage('/block_kyeongyoong_youtube.png');
		BLOCK_REKURIM_CHZZK = loadImage('/block_rekurim_chzzk.png');
		BLOCK_REKURIM_YOUTUBE = loadImage('/block_rekurim_youtube.png');
		PAW_BLUE = loadImage('/paw_blue.png');
		PAW_MINT = loadImage('/paw_mint.png');
		PAW_PURPLE = loadImage('/paw_purple.png');
		PAW_YELLOW = loadImage('/paw_yellow.png');
		PAW_GREY = loadImage('/paw_grey.png');
		ANNOUNCEMENT = loadImage('/announcement.png');

		ctx = canvas.getContext('2d')!;
		canvas.width = WIDTH;
		canvas.height = HEIGHT;
	});
</script>

<div class="container">
	<canvas bind:this={canvas}></canvas>
	<div class="controls">
		<div class="control-row">
			<div class="control-label">
				<label for="fromDate">월요일</label>
			</div>
			<div class="control-input"><input type="date" bind:value={fromDate} onchange={draw} /></div>
		</div>
		<div class="control-row">
			<div class="control-label">
				<label for="fromDate">발바닥</label>
			</div>
			<div class="control-input">
				<label><input type="checkbox" bind:checked={yellowPaw} onchange={draw} /> 노란</label>
				<label><input type="checkbox" bind:checked={mintPaw} onchange={draw} /> 민트</label>
				<label><input type="checkbox" bind:checked={bluePaw} onchange={draw} /> 파란</label>
				<label><input type="checkbox" bind:checked={purplePaw} onchange={draw} /> 보라</label>
			</div>
		</div>
		{#each [mondayPlans, tuesdayPlans, wednesdayPlans, thursdayPlans, fridayPlans, saturdayPlans, sundayPlans] as plans, dayIndex}
			<div class="control-row">
				<div class="plan-label">
					<label for="fromDate"
						>{['월', '화', '수', '목', '금', '토', '일'][dayIndex]}요일 일정</label
					>
				</div>
				<div class="control-input">
					{#each plans as plan, index}
						<div class="plan-inputs">
							<select bind:value={plan.who} onchange={draw}>
								<option>하울링크</option>
								<option>이다음</option>
								<option>연도진</option>
								<option>리쿠림</option>
								<option>경융</option>
							</select>
							<select bind:value={plan.platform} onchange={draw}>
								<option>치지직</option>
								<option>유튜브</option>
							</select>
							<input type="text" placeholder="제목" bind:value={plan.title} oninput={draw} />
							<input type="text" placeholder="부제목" bind:value={plan.subtitle} oninput={draw} />
							<input type="text" placeholder="12:00" bind:value={plan.time} oninput={draw} />
							<button
								onclick={() => {
									if (dayIndex === 0)
										mondayPlans = [...mondayPlans.slice(0, index), ...mondayPlans.slice(index + 1)];
									else if (dayIndex === 1)
										tuesdayPlans = [
											...tuesdayPlans.slice(0, index),
											...tuesdayPlans.slice(index + 1)
										];
									else if (dayIndex === 2)
										wednesdayPlans = [
											...wednesdayPlans.slice(0, index),
											...wednesdayPlans.slice(index + 1)
										];
									else if (dayIndex === 3)
										thursdayPlans = [
											...thursdayPlans.slice(0, index),
											...thursdayPlans.slice(index + 1)
										];
									else if (dayIndex === 4)
										fridayPlans = [...fridayPlans.slice(0, index), ...fridayPlans.slice(index + 1)];
									else if (dayIndex === 5)
										saturdayPlans = [
											...saturdayPlans.slice(0, index),
											...saturdayPlans.slice(index + 1)
										];
									else if (dayIndex === 6)
										sundayPlans = [...sundayPlans.slice(0, index), ...sundayPlans.slice(index + 1)];

									draw();
								}}
							>
								삭제
							</button>
						</div>
					{/each}
					<button
						onclick={() => {
							const newPlan = {
								who: '하울링크',
								platform: '치지직',
								title: '제목',
								subtitle: '부제목',
								time: '12:00'
							};
							if (dayIndex === 0) mondayPlans = [...mondayPlans, newPlan];
							else if (dayIndex === 1) tuesdayPlans = [...tuesdayPlans, newPlan];
							else if (dayIndex === 2) wednesdayPlans = [...wednesdayPlans, newPlan];
							else if (dayIndex === 3) thursdayPlans = [...thursdayPlans, newPlan];
							else if (dayIndex === 4) fridayPlans = [...fridayPlans, newPlan];
							else if (dayIndex === 5) saturdayPlans = [...saturdayPlans, newPlan];
							else if (dayIndex === 6) sundayPlans = [...sundayPlans, newPlan];

							draw();
						}}
					>
						일정 추가
					</button>
				</div>
			</div>
		{/each}
		<div class="control-row">
			<div class="plan-label">
				<label for="fromDate">전체 공지</label>
			</div>
			<div class="control-input">
				<input type="text" placeholder="공지사항을 입력하세요" bind:value={notice} oninput={draw} />
			</div>
		</div>
		<button onclick={draw}>새로고침</button>
		<button onclick={downloadCanvasAsImage}>다운로드</button>
	</div>
</div>

<style>
	.container {
		position: relative;
		width: 100%;
		display: flex;
		justify-content: center;
		flex-direction: column;
		max-width: 800px;
		margin: 12px auto;
	}

	canvas {
		border: 1px solid #ccc;
	}

	.controls {
		margin-top: 20px;
	}

	.control-row {
		display: flex;
		align-items: center;
		margin-bottom: 10px;
	}

	.control-label {
		margin-right: 10px;
		font-weight: bold;
	}

	.control-input {
		flex: 1;
	}

	.control-input input {
		padding: 5px;
		font-size: 16px;
	}

	.control-input input[type='text'],
	.control-input input[type='date'] {
		width: 100%;
	}

	button {
		padding: 10px 20px;
		font-size: 16px;
		background-color: #007bff;
		color: white;
		border: none;
		border-radius: 5px;
		cursor: pointer;
	}

	button:hover {
		background-color: #0056b3;
	}

	.plan-label {
		margin-right: 10px;
		min-width: 80px;
		font-weight: bold;
	}

	.plan-inputs {
		display: flex;
		align-items: center;
		gap: 10px;
		margin-bottom: 5px;
	}
</style>
