<script>
	// Import the required libraries
	import { onMount } from "svelte";
	import "intl-tel-input/build/css/intlTelInput.css";
	import intlTelInput from "intl-tel-input";
	import { afterUpdate } from "svelte";
  
	// State variables
	let phone = "";
	let selectedCountry = "";
	let countryFlags = [];
	let isValid=false; // Initialize the countryFlags array
  
	// Function to fetch default flags from the API
	async function fetchDefaultFlags() {
	  try {
		const response = await fetch("https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
		const data = await response.json();
  console.log(data);
		// Assuming the API returns an array of countries with 'flag' and 'code' properties
		phone = data.phone; // Adjust the property names based on the API response
	  } catch (error) {
		console.error("Error fetching default flags:", error);
	  }
	}
  
	// Call the function to fetch the default flags on component mount
	onMount(() => {
	  fetchDefaultFlags();
	});
  
 afterUpdate(() => {
    const inputElement = document.getElementById("phone-input");
    const intlTelInputInstance = intlTelInput(inputElement, {
      initialCountry: selectedCountry,
      utilsScript: "intl-tel-input/build/js/utils.js",
    });

    // Listen for changes to update the selectedCountry and phone
    inputElement.addEventListener("countrychange", () => {
      selectedCountry = inputElement.getAttribute("data-country-code");
      phone = intlTelInputInstance.getNumber(); // Get the formatted number with dial code

      // Remove the dial code from the phone number
      if (phone) {
        const dialCode = intlTelInputInstance.getSelectedCountryData().dialCode;
        phone = phone.replace(`+${dialCode}`, "").trim();
      }
    });
  });
  </script>


<div class="mb-6 flex flex-wrap">
	
	<div class="w-1/3 pr-1">
	  <div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		<select class="country-select {!isValid && 'invalid'} block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600" aria-label="Default select example" name="Country" bind:value={selectedCountry}>
		  <!-- Add a flag icon to the "Please select" option -->
		  <option >
			 Please select
		  </option>
		  {#each countryFlags as { flag, code }}
			<option value={code} selected={code === selectedCountry} aria-selected={code === selectedCountry}>
			  {flag} ({code})
			</option>
		  {/each}
		</select>
	  </div>
	</div>
	<div class="w-2/3 pl-1">
	  <div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		<!-- Remove the `country={selectedCountry}` attribute from the TelInput component -->
		<input
		  type="tel"
		  id="phone-input"
		  bind:value={phone}
		  class="form-input {!isValid && 'invalid'} block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600"
		  placeholder="Phone"
		/>
	  </div>
	</div>
  </div>
  <style>
	.iti__flag-container {
	  display: none !important;
	}
  
	.iti__selected-flag .iti__flag {
	  display: none;
	}
  </style>
