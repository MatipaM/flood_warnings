<script>
    import Link from './Link.svelte'
    import Input from './Input.svelte'

    let content = document.getElementById('content')
    content.textContent = ''

    let menuOpen=true
    let inputValue="";

    let h1 = document.createElement('h1')
    h1.textContent = "Today's Flood Warnings"
    content.appendChild(h1);
   

    let result_text;
    let regionMenu = [];
    let filteredItems = []
    let results;

    let cards = document.createElement('div')
    cards.id="card"
    cards.classList.add("cards")

    async function getApiData() {
      // Search the V&A API
      const query =
        'http://environment.data.gov.uk/flood-monitoring/id/floods' 
  
    //  let errorMsg = document.createElement('p');
    results = await fetch(query).then((res) => res.json());
    //  return results

     if(results!=undefined){
        populateRegionMenu()
        display()
     }
}

 // repeat cycling through data, removed this code from filterRegions to avoid having one function doing too many things. This function just populates the array of items to be cycle through in order to filter by region.
 function populateRegionMenu(){
        for(let idx = 0; idx< results['items'].length; idx++){
            for (const [key, value] of Object.entries(results['items'][idx])) {
                if(key=="eaAreaName"){
                    if(regionMenu.includes(value)==false){
                        regionMenu.push(value)
                    }
                }
            }
        }      
        // filterRegions(results)
        console.log(regionMenu)
        return regionMenu
        // display(regionMenu)
    }


    function display(){
        cards.textContent = ""
        for(let idx = 0; idx< results['items'].length; idx++){

            let article = document.createElement('article')
            article.classList.add("card")
           
            for (const [key, value] of Object.entries(results['items'][idx])) {
                let section = document.createElement('section')
                section.classList.add("card-content")
                let p = document.createElement("p")
               

                if(key=="eaRegionName"){
                    continue
                }
                else if(key=="floodArea"){
                    result_text=""
                    result_text+=key+ ": "
                    for (const [key, val] of Object.entries(value)) {
                        result_text += key+":"+val
                    }
                }
                else{
                    result_text = key + ":" + value
                }

                if(key=="description"){
                    section.classList.add("strong")
                }

                // section.classList.add("card-content")
                
                console.log(p)
                p.textContent += result_text
                section.append(p)
                article.appendChild(section)
                cards.appendChild(article);
            }
                }
               
            content.appendChild(cards)
            // regionMenu = populateRegionMenu()
        
        // }   
    }

    function filterMenu(pages){
        cards.textContent = ""
        for(let idx = 0; idx< results['items'].length; idx++){

            let article = document.createElement('article')
            article.classList.add("card")
            if(pages.includes(results['items'][idx].eaAreaName)==true){
              

            
            for (const [key, value] of Object.entries(results['items'][idx])) {
                let section = document.createElement('section')
                section.classList.add("card-content")
                let p = document.createElement("p")
                // eaRegionName no longer used
                if(key=="eaRegionName"){
                    continue
                }
                else if(key=="floodArea"){
                    result_text="Today's Floor Warnings"
                    result_text+=key+ ": "
                    for (const [key, val] of Object.entries(value)) {
                        result_text += key+":"+val
                    }
                }
                else{
                    result_text = key + ":" + value
                }

                if(key=="description"){
                    section.classList.add("strong")
                }
                
                console.log(p)
                p.textContent += result_text
                section.append(p)
                article.appendChild(section)
                cards.appendChild(article);
            }
                }
               
            content.appendChild(cards) 
        }
    }

    const handleInput = () => {
        filteredItems = regionMenu.filter(item => item.toLowerCase().match(inputValue.toLowerCase()));

        console.log("filteredItems", filteredItems)
        filterMenu(filteredItems)
        return filteredItems

        // filteredItems = regionMenu.filter(item => item.toLowerCase().match(inputValue.toLowerCase()));
        // console.log(filteredItems)
        // filterRegions(filteredItems)
        // return filteredItems
    }

</script>

{getApiData()}

<section class="dropdown">
    <!-- <Button on:click={() => menuOpen = !menuOpen} {menuOpen} /> -->
        <!-- <Button on:click={() => menuOpen} /> -->
      
    <div id="myDropdown" class:show={menuOpen} class="dropdown-content">		
    <Input bind:inputValue on:input={handleInput} />		
          {#if filteredItems.length > 0}
              {#each filteredItems as item}
                  <Link link={item} />
              {/each}
          {:else}

              {#each regionMenu as item}
              {console.log(regionMenu)}
                  <Link link={item} />
              {/each}
          {/if}		
    </div>	
  </section>