<script>
	import { onMount } from 'svelte';

	const random = (min, max) => {
		min = Math.ceil(min);
		max = Math.floor(max);
		return Math.floor(Math.random() * (max - min) + min);
	};

	const charsInRange =  (min, max) => {
		let chars = '';
		for (let i = min; i <= max; i++) {
			chars += String.fromCharCode(i);
		}
		return chars;
	};

	const numbersString = charsInRange(48, 57);
	const lowercaseString = charsInRange(97, 122);
	const uppercaseString = charsInRange(65, 90);
	const symbolsString = charsInRange(33, 47) +
		charsInRange(58, 64) +
		charsInRange(91, 96) +
		charsInRange(123, 126);

	let passwordInput;
	let password = '';
	let copyDisabled = false;
	let copyText = 'Copy';
	let includeNumbers = true;
	let includeLowercase = true;
	let includeUppercase = true;
	let includeSymbols = true;
	let length = 16;

	let copyTimeoutId;

	const handleCopy = () => {
		window.clearTimeout(copyTimeoutId);

		passwordInput.select();
		passwordInput.setSelectionRange(0, 99999);
		document.execCommand("copy");

		copyText = 'Copied!';
		copyDisabled = true;

		copyTimeoutId = window.setTimeout(() => {
			copyText = 'Copy';
			copyDisabled = false;
		}, 1000);
	};

	const handleGenerate = () => {
		if (!(includeNumbers || includeLowercase || includeUppercase || includeSymbols)) return;

		const len = parseInt(String(length), 10);
		if (isNaN(len) || len < 1) return;

		const chars = (includeNumbers ? numbersString : '') +
			(includeLowercase ? lowercaseString : '') +
			(includeUppercase ? uppercaseString : '') +
			(includeSymbols ? symbolsString : '');

		let tmp = '';
		for (; tmp.length < len;) {
			tmp += chars[random(0, chars.length)];
		}

		password = tmp;
	};

	onMount(handleGenerate);
</script>

<main>
	<h1>Erik's Secure Password Generator</h1>
	<div class="options">
		<label>
			<input
                type="checkbox"
                bind:checked={includeNumbers}
                on:change={handleGenerate}>
			<span>Include numbers</span>
		</label>
		<label>
			<input
                type="checkbox"
                bind:checked={includeLowercase}
                on:change={handleGenerate}>
			<span>Include lowercase characters</span>
		</label>
		<label>
			<input
                type="checkbox"
                bind:checked={includeUppercase}
                on:change={handleGenerate}>
			<span>Include uppercase characters</span>
		</label>
		<label>
			<input
                type="checkbox"
                bind:checked={includeSymbols}
                on:change={handleGenerate}>
			<span>Include symbols</span>
		</label>
		<label>
			<span>Length:</span>
			<input
                type="number"
                step="1"
                bind:value={length}
                on:change={handleGenerate}>
		</label>
		<button on:click={handleGenerate}>
			Generate
		</button>
	</div>
	<div>
		<label>
			<span>Your generated password:</span>
			<input
                type="text"
                bind:value={password}
                bind:this={passwordInput}>
		</label>
		<button
            on:click={handleCopy}
            disabled={copyDisabled}>
			{copyText}
		</button>
	</div>
	<p>It is recommended to store this password in a password manager of your choice (e.g. <a href="https://support.apple.com/en-us/HT204085">iCloud Keychain</a>, <a href="https://passwords.google.com">Google Password Manager</a>, <a href="https://1password.com">1Password</a> or <a href="https://www.dashlane.com">Dashlane</a>).
	<p>Password is generated on the client-side and is never sent to a server.</p>
	<p>This is an open-source project <a href="https://github.com/erikwritescode/passgen/blob/master/LICENSE">licensed under MIT</a> and accessible on <a href="https://github.com/erikwritescode/passgen">GitHub</a>.</p>
	<p>Made in 2020 by a Geek🤓 and hosted on <a href="https://www.netlify.com">Netlify</a>.</p>
</main>

<style>
	
</style>
