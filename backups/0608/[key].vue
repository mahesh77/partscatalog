<script setup>

    import { useRoute } from 'vue-router'

const route = useRoute()

// Fetch product details using key from the URL
 const { data, error } = await useFetch(`/api/${route.params.key}`, {
  lazy: false,
  server: true
})

const product = computed(() => data.value?.products || [])


const applicationsRaw = product.value?.application
// Parse & filter
const applicationNames = computed(() => {
  try {
    const apps = JSON.parse(applicationsRaw)
    return Object.keys(apps)
      .filter(key => apps[key] === "TRUE")   // only TRUE values
      .map(key => key.replace(/^app_/, '').replace(/_/g, ' ').replace(/\b\w/g, c => c.toUpperCase())) 
      // e.g. "app_defense" ? "Defense"
  } catch {
    return []
  }
  })


// helper: format feature keys
function formatLabel(key) {
  return key
    .replace(/^ft_/, '')       // remove ft_ prefix
    .replace(/_/g, ' ')        // underscores ? spaces
    .replace(/^\w/, c => c.toUpperCase()) // capitalize first letter
}
const featureList = computed(() => {
  let features = product.value?.features
  console.log(features)
  if (!features) return []

  // Parse string if necessary
  if (typeof features === 'string') {
    try {
      features = JSON.parse(features)
	  console.log(features)
    } catch (err) {
      console.error('Failed to parse features JSON:', err)
      return []
    }
  }

  return Object.entries(features).map(([key, value]) => ({
    key,
    label: formatLabel(key),
    value
  }))
})
</script>

<template class="!bg-white">
    <section class="w-full max-w-7xl">
		 <hgroup class="w-full p-4 sticky  top-0 left-0 main-background">
        	<span class="large text-white text-[26px] left-[39px] ">exo<span class="exo font-bold text-[30px]">search</span></span>
		   <!-- <p>The technical search engine for industry.</p>-->
		   <div class=" mt-4 float-right text-white">
			<ul class="flex flex-wrap gap-2 ">
			  <li class="inline-block"><a href="#">All products</a></li>
			  <li class="inline-block"><a href="#">About</a></li>
			  <li class="inline-block"><a href="#">Vendor sign-in</a></li>
			</ul>
		   </div>
		</hgroup>
		</section>
		<section class="relative mt-[3rem] left-[39px]">
		<div v-if="product" class="">
        <div class="mb-6 flex items-center gap-3">
			<h3 class="font-bold text-[24px]">{{ product.manufacturer }} {{product.model}}</h3> 
			<!-- custom label -->
			
			<label class="cursor-pointer text-[#1728e5] bg-[#e2eafa] border-0 no-underline text-[17px]] pr-[11px] pl-[8px]"
			>
			 {{ product.type }}
			</label>
			
        </div>
		<div class="relative -top-[10px]">
		<span v-for="app in applicationNames" :key="app" class="font-[400] text-[#1728e5] bg-[#e2eafa] border-0 no-underline text-[17px]] pr-[8px] pl-[8px] gap-4 mr-[5px] pb-[3px]">{{ app }}</span> <span>{{product.country_of_origin}}</span>
		</div>
       	</div>
		<div class="relative top-[40px]">
		<Button label="Request quote" class="bg-[#1728e5] mt-[10px]"></Button> &nbsp;&nbsp;&nbsp;{{product.website}}  
	   	</div>
		<div class="overflow-x-auto mt-[70px]">
			<table class="min-w-[700px] border-collapse border border-gray-300 ">
				  <thead>
				   <tr class="bg-gray-100">
				   <th class="border border-gray-300 px-4 py-3 text-left sticky left-0 bg-gray-100 z-10">Product</th>
					<th v-for="item in featureList" :key="item.key" class="border border-gray-300 px-4 py-3 text-left whitespace-nowrap">{{ item.label }}</th>
					</tr>
				  </thead>
				  <tbody>
				  <tr>
					<td class="border border-gray-300 p-2"> {{ product.manufacturer }} {{product.model}}</td>
				  
					<td v-for="item in featureList" :key="item.key" class="border border-gray-300 p-2">
					   {{ item.value }}</td>
					</tr>
				  </tbody>
			</table>
	</div>
	
    </section>
</template>

<style scoped>

h1 {
    grid-column: 1 / span 12;
    grid-row: 1;
}

form {
    grid-column: 1 / span 12;
    grid-row: 2;
    display: flex;
    flex-direction: column;
    gap: 12px;
    align-items: start;
}

main {
    grid-column: 1 / span 12;
    grid-row: 3;
}

.cards-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
}

.my-table :deep(.p-datatable-table) {
  table-layout: auto !important;
  width: auto;
  white-space: nowrap;
  td, th {
    padding: 4px 12px 4px 12px;
  }
}

.tags-cell {
    display: flex;
    gap: 2px;
}

</style>