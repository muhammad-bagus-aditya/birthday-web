<script lang="ts">
	import { fade } from 'svelte/transition';
	import { gsap } from 'gsap';
	import { TextPlugin } from 'gsap/TextPlugin';
	import { timestamp, Birthday } from '$lib/birthday';
	import { twMerge } from 'tailwind-merge';
	import Swal from 'sweetalert2';

	let audio: HTMLAudioElement;
	let question: HTMLHeadingElement;

	let stopFireworks = $state(false);

	let showQuestion = $state(true);

	let text = $state('Apakah kamu yang bernama Aida Aprila');
	let failed = $state(false);

	let showButtons = $state(false);

	let showScreenOne = $state(false);
	let showScreenTwo = $state(false);
	let showScreenThree = $state(false);

	const blowerAudio = new Audio();
	blowerAudio.loop = true;
	blowerAudio.volume = 0.2;
	blowerAudio.src = '/music/party-blower.mp3';

	const animateScreenOne = () => {
		const tl = gsap.timeline();

		tl.fromTo(
			'#text-one',
			{
				translateY: '-20px',
				opacity: 0
			},
			{
				translateY: 0,
				opacity: 1
			}
		);

		tl.fromTo(
			'#text-two',
			{
				translateY: '-20px',
				opacity: 0
			},
			{
				delay: 1,
				translateY: 0,
				opacity: 1
			}
		);

		tl.to('#text-one', {
			delay: 2,
			translateY: '20px',
			opacity: 0
		});

		tl.to('#text-two', {
			translateY: '20px',
			opacity: 0
		});

		setTimeout(() => {
			showScreenOne = false;
			showScreenTwo = true;

			setTimeout(() => {
				animateScreenTwo();
			}, 1000);
		}, 6000);
	};

	const animateScreenTwo = () => {
		const tl = gsap.timeline();

		tl.fromTo(
			'#text-three',
			{
				translateY: '-20px',
				opacity: 0
			},
			{
				translateY: 0,
				opacity: 1
			}
		);

		tl.to('#text-three', {
			delay: 2,
			translateY: '20px',
			opacity: 0
		});

		tl.fromTo(
			'#text-four',
			{
				translateY: '-20px',
				opacity: 0
			},
			{
				translateY: 0,
				opacity: 1
			}
		);

		tl.to('#text-four', {
			delay: 2,
			translateY: '20px',
			opacity: 0
		});

		tl.fromTo(
			'#text-five',
			{
				translateY: '-20px',
				opacity: 0
			},
			{
				translateY: 0,
				opacity: 1
			}
		);

		tl.to('#text-five', {
			delay: 4,
			translateY: '20px',
			opacity: 0
		});

		tl.fromTo(
			'#text-six',
			{
				translateY: '-20px',
				translateX: '-20px',
				opacity: 0
			},
			{
				translateY: 0,
				translateX: 0,
				opacity: 1
			}
		);

		tl.fromTo(
			'#text-seven',
			{
				translateY: '-20px',
				translateX: '20px',
				opacity: 0
			},
			{
				delay: 0.5,
				translateY: 0,
				translateX: 0,
				opacity: 1
			}
		);

		tl.to('#text-six', {
			delay: 2,
			translateY: '20px',
			opacity: 0
		});

		tl.to('#text-seven', {
			translateY: '20px',
			opacity: 0
		});

		setTimeout(() => {
			showScreenTwo = false;
			showScreenThree = true;

			setTimeout(() => {
				animateScreenThree();
			}, 1000);
		}, 16000);
	};

	const animateScreenThree = () => {
		audio.pause();

		// audio.src = '/music/fireworks.mp3';
		// audio.play();

		blowerAudio.play();

		// for (let i = 0; i <= 100; i++) {
		// 	audio.volume = i / 100;
		// }

		const tl = gsap.timeline();

		tl.fromTo(
			'#img',
			{
				opacity: 0
			},
			{
				opacity: 1
			}
		);

		tl.to('#text-eight', {
			opacity: 1
		});

		tl.to('#text-nine', {
			opacity: 1
		});

		tl.to('#text-ten', {
			text: 'Semoga panjang umur, sehat selalu, diberikan jalan yang terbaik, dimudahkan urusannya, dikabulkan keinginannya, dan sukses dunia akhirat.',
			ease: 'none',
			duration: 8
		});

		tl.fromTo(
			'#continue-btn',
			{
				opacity: 0,
				rotateY: -360
			},
			{
				opacity: 1,
				rotateY: 0
			}
		);

		document.querySelectorAll('.baloons img').forEach((baloon, i) => {
			tl.fromTo(
				baloon,
				2.5,
				{
					opacity: 0.9,
					y: 1400
				},
				{
					delay: 0.2 * i,
					opacity: 1,
					y: -1400
				},
				0.2
			);
		});

		let canvas: HTMLCanvasElement = document.getElementById('birthday') as HTMLCanvasElement;
		let ctx = canvas.getContext('2d');

		let then = timestamp();

		let birthday = new Birthday(canvas, ctx);

		window.onresize = () => birthday.resize();
		document.onclick = (evt) => birthday.onClick(evt);
		document.ontouchstart = (evt) => birthday.onClick(evt);
		(function loop() {
			if (stopFireworks) return;
			requestAnimationFrame(loop);

			let now = timestamp();
			let delta = now - then;

			then = now;
			birthday.update(delta / 1000);
		})();
	};

	const clickYesButton = () => {
		showQuestion = false;
		showScreenOne = true;

		setTimeout(() => {
			animateScreenOne();
		}, 1000);
	};

	const clickNoButton = () => {
		text = 'Maaf ini special buat Aida';
		failed = true;
	};

	$effect(() => {
		Swal.fire({
			title: 'Izinkan autoplay',
			text: 'untuk mengaktifkannya, silahkan cek di setting anda atau di '
		});

		audio.loop = true;
		audio.src = '/music/lobby.mp3';
		audio.volume = 0.2;
		audio?.play();

		gsap.registerPlugin(TextPlugin);

		const tl = gsap.timeline();

		tl.from(question, {
			translateY: '20px'
		});

		tl.from('#buttons', {
			opacity: 0
		});

		setTimeout(() => {
			showButtons = true;
		}, 7000);

		return () => {
			audio.pause();
			blowerAudio.pause();
			stopFireworks = true;
		};
	});
</script>

<svelte:head>
	<title>HBD FOR AIDA</title>

	<link
		rel="stylesheet"
		href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css"
	/>
</svelte:head>

<audio bind:this={audio}></audio>

{#if showQuestion}
	<div class="pt-60 text-slate-200" out:fade>
		<h1
			class={twMerge(
				'mx-auto w-[50%] text-center text-5xl leading-16',
				failed ? 'text-red-600' : ''
			)}
			bind:this={question}
		>
			{text}
		</h1>

		<div class="mt-12 flex justify-center gap-8" id="buttons">
			<button
				class="w-48 rounded-full border-2 border-emerald-600 px-8 py-4 text-3xl text-emerald-600 transition-all duration-500 hover:bg-emerald-600 hover:text-slate-200"
				onclick={clickYesButton}
			>
				Iya
			</button>

			<button
				class="w-48 rounded-full border-2 border-red-600 px-8 py-4 text-3xl text-red-600 transition-all duration-500 hover:bg-red-600 hover:text-slate-200"
				onclick={clickNoButton}
			>
				Tidak
			</button>
		</div>
	</div>
{/if}

{#if showScreenOne}
	<div
		class="flex min-h-screen flex-col items-center justify-center text-slate-200"
		id="screen-one"
	>
		<div class="flex flex-col gap-6 opacity-0" id="text-one">
			<h1 class="text-center text-6xl">
				Halo
				<span id="name">Aida Aprila</span>
			</h1>
		</div>

		<div id="text-two" class="opacity-0">
			<p class="text-6xl">Hari ini ulang tahunmu!! :D</p>
		</div>
	</div>
{/if}

{#if showScreenTwo}
	<div class="flex min-h-screen flex-col items-center justify-center text-slate-200">
		<div class="fixed top-0 left-0 flex h-full w-full items-center justify-center">
			<p class="text-center text-6xl opacity-0" id="text-three">
				Aku punya sesuatu yang <br />
				<strong>special</strong>
				untukmu
			</p>
		</div>

		<div class="fixed top-0 left-0 flex h-full w-full items-center justify-center">
			<p class="text-6xl opacity-0" id="text-four">Tapi,</p>
		</div>

		<div class="fixed top-0 left-0 flex h-full w-full items-center justify-center">
			<p class="text-6xl opacity-0" id="text-five">
				Tunggu nanti sore yah
				<span>:D</span>
			</p>
		</div>

		<div class="fixed top-0 left-0 flex h-full w-full items-center justify-center">
			<p class="flex text-6xl">
				<span id="text-six" class="opacity-0">JA</span>
				<span id="text-seven" class="opacity-0">DI</span>
			</p>
		</div>
	</div>
{/if}

{#if showScreenThree}
	<canvas id="birthday" class="fixed top-0 left-0 z-[-1] h-screen w-screen"></canvas>

	<div
		class="fixed top-0 left-0 flex min-h-screen w-full flex-col items-center justify-center text-slate-200"
	>
		<div class="flex flex-col items-center gap-4">
			<img
				src="/img/person.jpg"
				class="size-64 rounded-full object-cover object-center opacity-0"
				alt=""
				id="img"
			/>

			<div class="wish flex flex-col gap-3 text-center">
				<h3 class="wish-hbd text-6xl opacity-0" id="text-eight">Happy Birthday</h3>
				<h5 id="text-nine" class="text-3xl opacity-0">Aida Aprila Salsabila</h5>
				<p id="text-ten" class="mx-auto w-[60%] text-xl"></p>
			</div>

			<button class="rounded-full px-12 py-4 text-2xl opacity-0" id="continue-btn">
				<a href="/video">
					<i class="bi bi-chevron-double-right"></i> Aamiin!
					<i class="bi bi-chevron-double-left"></i>
				</a>
			</button>
		</div>

		<div class="seven" id="baloons">
			<div class="baloons">
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon2.svg" alt="" />
				<img src="img/ballon1.svg" alt="" />
				<img src="img/ballon3.svg" alt="" />
			</div>
		</div>
	</div>
{/if}
