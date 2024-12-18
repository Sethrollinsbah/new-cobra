<script lang="ts">
	import Contact from '$lib/markdown-content/Contact.svelte';
	import faq from '$lib/faq.json';
	import Text from '$lib/text.svelte';
	import Quiz from '$lib/quiz.svelte';
	import type { PageData } from './$types';
	import { goto } from '$app/navigation';

	import { onMount } from 'svelte';

	const questions = [
		{
			key: 'name',
			type: 'name' as const,
			title: 'What is your first and last name?',
			validation: (value: { firstName: string; lastName: string }) =>
				value?.firstName?.length > 0 && value?.lastName?.length > 0,
			errorMessage: 'Please enter your full name'
		},
		{
			key: 'household',
			type: 'choice' as const,
			title: 'What is your household size?',
			choices: ['1', '2', '3', '4', '5+']
		},
		{
			key: 'age',
			type: 'age' as const,
			title: 'What is your DOB?',
			validation: (value: DateValue) => value !== null,
			errorMessage: 'Please select your date of birth'
		},
		{
			key: 'zip',
			type: 'zip' as const,
			title: 'What is your zip code?',
			validation: (value: string) => /^\d{5}$/.test(value),
			errorMessage: 'Please enter a valid zip code'
		},
		{
			key: 'phone',
			type: 'phone' as const,
			title: 'What is your phone number?',
			validation: (value: { code: string; number: string }) =>
				value?.number?.replace(/[^0-9]/g, '').length === 10,
			errorMessage: 'Please enter a valid phone number'
		},
		{
			key: 'email',
			type: 'email' as const,
			title: 'What is your email?',
			validation: (value: string) => /^[\w.-]+@[a-zA-Z\d.-]+\.[a-zA-Z]{2,}$/.test(value),
			errorMessage: 'Please enter a valid email address'
		},
		{
			key: 'pregnant',
			type: 'choice' as const,
			title: 'Are you and your spouse currently pregnant?',
			choices: ['Yes', 'No']
		}
	];

	export let data: PageData;
	let company = ['/aetna.png', '/cigna.png', '/blue_cross.png', '/united_healthcare.png'];

	// Reactive array to control the display based on showMore
	import FAQ from '$lib/FAQ.svelte';
</script>

<!-- Header -->

<div class="relative h-[100dvh]">
	<div class="relative h-full w-full">
		<div
			class={`absolute left-0 top-0 h-[85dvh] w-full overflow-clip bg-cover bg-center`}
			style={`background-image: url(/bg.webp)`}
		>
			<div
				class={`flex h-full w-full flex-col items-center justify-center bg-black/80 backdrop-blur-sm`}
			>
				<div
					id="enroll"
					class="m-auto flex flex-col items-center justify-center text-4xl font-semibold"
				>
					<Text
						className="m-auto text-gray-200 text-4xl sm:text-5xl md:text-6xl text-center font-semibold"
						>COBRA Solutions<br /></Text
					>
					<Text
						className="m-auto text-gray-200/80 text-lg  max-w-md w-[90%] md:text-xl text-center font-light"
						>Learn about COBRA Insurance alternatives and get a quote today</Text
					>
				</div>
			</div>
		</div>
	</div>
	<Quiz
		{questions}
		onSubmit={async (answers) => {
			try {
				// Assuming `form` contains the necessary form data
				const data = answers;
				// Sending the data to the backend API endpoint
				const res = await fetch('/api/upload_lead', {
					method: 'POST',
					headers: {
						'Content-Type': 'application/json' // Ensure correct content type
					},
					body: JSON.stringify({ data, type: 'Lead' })
				});
				// Check if the request was successful
				if (res.ok) {
					console.log('Lead uploaded successfully');
					goto(`/~/completion?name=${data.name.firstName}`); // Navigate to the completion page
				} else {
					console.error('Error uploading lead:', await res.text());
					// Handle any errors from the API
				}
			} catch (error) {
				console.error('Error during submission:', error);
				// Handle unexpected errors
			}
		}}
	/>
</div>

<!-- Companies we work with -->
<div class="mx-auto grid w-[90%] max-w-xl grid-cols-2 items-center justify-evenly pt-16 sm:flex">
	{#each company as c}
		<img alt="insurance company" src={c} class="mx-auto size-16 object-contain" />
	{/each}
</div>

<div class="bg-transparent">
	<div class="mx-auto w-[90%] max-w-3xl py-12">
		<h1>What Is COBRA?</h1>
		<p>
			The Consolidated Omnibus Budget Reconciliation Act (COBRA) was signed into law on April 7,
			1986 by Ronald Reagan. COBRA allows former employees to continue their current health coverage
			after leaving their job, which ensures uninterrupted healthcare access and provides the same
			benefits while retaining the same doctors they had while employed. This law was designed to
			help individuals maintain health coverage during job transitions, providing a safety net for
			millions of Americans.
		</p>

		<h1>How Does COBRA Work?</h1>
		<p>
			When a former employee chooses to opt in for COBRA, they agree to pay the full cost of their
			health insurance plan, including the portion their employer previously contributed, plus a 2%
			service fee. By opting in, they will be able to retain the same plan they had while employed,
			with the same network of doctors and coverage benefits.
		</p>

		<h1>COBRA Qualifications</h1>
		<p>There are many ways for someone to qualify for COBRA. These include:</p>

		<ul>
			<li>
				Voluntary or involuntary loss of employment, which removes access to employer-sponsored
				health coverage
			</li>
			<li>
				A reduction in hours worked that results in the employee no longer being eligible for their
				employer's health insurance coverage
			</li>
			<li>
				A divorce that leads to the loss of health coverage previously provided through a spouse's
				employer-sponsored plan
			</li>
			<li>
				Death of a spouse, which results in the surviving spouse losing access to health coverage
				that was provided through the deceased spouse's employer
			</li>
			<li>
				Reaching the maximum age limit (which varies by state) to remain as a dependent on a
				parent's employer-sponsored health insurance plan
			</li>
		</ul>
		<h2>Duration and Cost of COBRA</h2>
		<p>
			COBRA coverage is temporary, and can last between 18 and 36 months, depending on the reason
			for losing your previous coverage. COBRA coverage also typically costs double the price of
			your previous plan, and can reach up to 4 times the price of your previous plan! This makes
			COBRA coverage unaffordable for the average American worker, especially a worker who has their
			family covered under their employer plan.
		</p>
	</div>
</div>

<div id="alternatives" class="grid grid-cols-1 bg-blue-100 py-12">
	<div class="mx-auto w-[90%] max-w-3xl">
		<h2>Alternatives to COBRA</h2>

		<p>
			Considering COBRA's short duration of coverage, and extremely high price, most people who it
			is offered to seek out alternative options to prevent overpaying and ensure that them and
			their family are covered long term. The best alternatives for COBRA include Public Market
			(ACA) plans and Private Market plans.
		</p>

		<p>
			Public Market plans are best suited for individuals with extremely low income, or poor health.
			These options offer minimal benefits with high out of pocket costs, but cannot deny you
			coverage for pre-existing conditions, and can protect you from bankruptcy in the case of a
			major hospitalization.
		</p>

		<p>
			Private Market plans are best suited for people in relatively good health, and have may
			exclude coverage for applicants who are very unhealthy. These options offer reduced rates with
			increased benefits and lower out of pocket costs, while still protecting you from bankruptcy
			in the case of a major hospitalization.
		</p>
	</div>
</div>

<!-- Why us over them -->
<div id="proscons" class="mx-auto w-[90%] max-w-4xl py-12">
	<h1>Pros and Cons of COBRA Insurance</h1>

	{#if faq}
		<FAQ title="General Questions" faq={Object.entries(faq)}></FAQ>
	{/if}
</div>

<Contact />

<style>
	/* Reset margins for consistent spacing */
	h1,
	h2,
	h3,
	h4,
	h5,
	h6,
	p {
		margin: 0;
		padding: 0;
		line-height: 1.5;
		color: #333333;
		font-family:
			system-ui,
			-apple-system,
			BlinkMacSystemFont,
			'Segoe UI',
			Roboto,
			Oxygen,
			Ubuntu,
			Cantarell,
			sans-serif;
	}

	/* Heading Styles */
	h1 {
		font-size: 2.5rem; /* 40px */
		font-weight: 700;
		margin-bottom: 1.5rem;
		letter-spacing: -0.025em;
	}

	h2 {
		font-size: 2rem; /* 32px */
		font-weight: 600;
		margin-bottom: 1.25rem;
		letter-spacing: -0.0125em;
	}

	h3 {
		font-size: 1.75rem; /* 28px */
		font-weight: 600;
		margin-bottom: 1rem;
	}

	h4 {
		font-size: 1.5rem; /* 24px */
		font-weight: 500;
		margin-bottom: 1rem;
	}

	h5 {
		font-size: 1.25rem; /* 20px */
		font-weight: 500;
		margin-bottom: 0.75rem;
	}

	h6 {
		font-size: 1.125rem; /* 18px */
		font-weight: 500;
		margin-bottom: 0.75rem;
	}

	/* Paragraph Styles */
	p {
		font-size: 1rem; /* 16px */
		font-weight: 400;
		margin-bottom: 1rem;
		line-height: 1.7;
	}

	/* Responsive adjustments for smaller screens */
	@media (max-width: 768px) {
		h1 {
			font-size: 2rem;
		}

		h2 {
			font-size: 1.75rem;
		}

		h3 {
			font-size: 1.5rem;
		}

		h4 {
			font-size: 1.25rem;
		}

		h5 {
			font-size: 1.125rem;
		}

		h6 {
			font-size: 1rem;
		}

		p {
			font-size: 0.9375rem;
		}
	}
	/* Unordered list styles */
	ul {
		margin: 1.5rem 0;
		padding-left: 1.5rem;
	}

	ul li {
		font-size: 1.125rem;
		margin-bottom: 1rem;
		padding-left: 0.5rem;
		position: relative;
	}

	/* Custom bullet points */
	ul li::before {
		content: '';
		position: absolute;
		left: -1.25rem;
		top: 0.75rem;
		width: 6px;
		height: 6px;
		background-color: #4a5568;
		border-radius: 50%;
	}

	/* Responsive adjustments */
	@media (max-width: 768px) {
		.markdown-content {
			padding: 1rem;
		}

		.markdown-content h1 {
			font-size: 1.875rem;
		}

		.markdown-content p,
		.markdown-content ul li {
			font-size: 1rem;
		}
	}

	/* Print styles */
	@media print {
		.markdown-content {
			max-width: none;
			padding: 0;
		}

		.markdown-content h1 {
			border-bottom: 1px solid #000;
		}

		.markdown-content ul li::before {
			background-color: #000;
		}
	}
</style>
