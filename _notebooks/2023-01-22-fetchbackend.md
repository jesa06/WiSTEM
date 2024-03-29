---
title: Backend to Frontend Users
layout: default
description: Making a table
permalink: /data/users
---

{% include nav_data.html %}

<!-- HTML table fragment for page -->
<table>
  <thead>
  <tr>
    <th>Name</th>
    <th>Airline</th>
    <th>Hotel</th>
    <th>Date of Birth</th>
    <th>Age</th>
    <th>Duration</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- javascript generated data -->
  </tbody>
</table>

<!-- Script is layed out in a sequence (without a function) and will execute when page is loaded -->
<script>

  // prepare HTML defined "result" container for new output
  const resultContainer = document.getElementById("result");

  // keys for joke reactions
  const NAME = "name";
  const AIRLINE = "airline";
  const HOTEL = "hotel";
  const DOB = "date of birth";
  const AGE = "age";
  const DURATION = "duration";


  // prepare fetch urls
  const url = "http://192.168.95.39:8086/api/users/";

  // prepare fetch GET options
  const options = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'omit', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
  };
  // prepare fetch PUT options, clones with JS Spread Operator (...)
  const put_options = {...options, method: 'PUT'}; // clones and replaces method

  // fetch the API
  fetch(url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error('GET API response failure: ' + response.status);
          return;
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          for (const row of data) {
            // make "tr element" for each "row of data"
            const tr = document.createElement("tr");
            
            // td for joke cell
            const joke = document.createElement("td");
              joke.innerHTML = row.id + ". " + row.joke;  // add fetched data to innerHTML

            // td for haha cell with onclick actions
            const haha = document.createElement("td");
              const haha_but = document.createElement('button');
              haha_but.id = HAHA+row.id   // establishes a HAHA JS id for cell
              haha_but.innerHTML = row.haha;  // add fetched "haha count" to innerHTML
              haha_but.onclick = function () {
                // onclick function call with "like parameters"
                reaction(HAHA, like_url+row.id, haha_but.id);  
              };
              haha.appendChild(haha_but);  // add "haha button" to haha cell

            // td for boohoo cell with onclick actions
            const boohoo = document.createElement("td");
              const boohoo_but = document.createElement('button');
              boohoo_but.id = BOOHOO+row.id  // establishes a BOOHOO JS id for cell
              boohoo_but.innerHTML = row.boohoo;  // add fetched "boohoo count" to innerHTML
              boohoo_but.onclick = function () {
                // onclick function call with "jeer parameters"
                reaction(BOOHOO, jeer_url+row.id, boohoo_but.id);  
              };
              boohoo.appendChild(boohoo_but);  // add "boohoo button" to boohoo cell
             
            // this builds ALL td's (cells) into tr (row) element
            tr.appendChild(joke);
            tr.appendChild(haha);
            tr.appendChild(boohoo);

            // this adds all the tr (row) work above to the HTML "result" container
            resultContainer.appendChild(tr);
          }
      })
  })
  // catch fetch errors (ie Nginx ACCESS to server blocked)
  .catch(err => {
    error(err + " " + url);
  });

  // Reaction function to likes or jeers user actions
  function reaction(type, put_url, elemID) {

    // fetch the API
    fetch(put_url, put_options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error("PUT API response failure: " + response.status)
          return;  // api failure
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          // Likes or Jeers updated/incremented
          if (type === HAHA) // like data element
            document.getElementById(elemID).innerHTML = data.haha;  // fetched haha data assigned to haha Document Object Model (DOM)
          else if (type === BOOHOO) // jeer data element
            document.getElementById(elemID).innerHTML = data.boohoo;  // fetched boohoo data assigned to boohoo Document Object Model (DOM)
          else
            error("unknown type: " + type);  // should never occur
      })
    })
    // catch fetch errors (ie Nginx ACCESS to server blocked)
    .catch(err => {
      error(err + " " + put_url);
    });
    
  }

  // Something went wrong with actions or responses
  function error(err) {
    // log as Error in console
    console.error(err);
    // append error to resultContainer
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  }

</script>
