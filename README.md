
//  ==UserScript==
// @name         VFS VISA NOVO
// @namespace    http://tampermonkey.net/
// @version      0.6
// @author       DESCONHECIDO
// @match        https://visa.vfsglobal.com/*
// @grant        none
// ==/UserScript==

(function () {
  'use strict';

  const expirationDate = new Date("2032-12-31");
  const clients = [

{

    'firstName': "",
    'lastName': "",

  },

          {
    'passportNumber': "N3420081",
    'birthDate': "13/06/1990",
    'passportExpiration': "02/09/2039",
    'firstName': "ANTONIO",
    'lastName': "MICOLO",
    'gender': '1',
    'country': "ANGOLA",
    'phone': "937574306",
    'email': "mikasamoreco@gmail.com",
    'type': "5956",
    'countryCode': '244'
  },
          {
    'passportNumber': "N3382629",
    'birthDate': "14/06/2003",
    'passportExpiration': "01/08/2034",
    'firstName': "JORGE",
    'lastName': "CASIMIRO",
    'gender': '1',
    'country': "ANGOLA",
    'phone': "937574306",
    'email': "mikasamoreco@gmail.com",
    'type': "5956",
    'countryCode': '244'
  },
          {
    'passportNumber': "N3494361",
    'birthDate': "07/01/1982",
    'passportExpiration': '07/11/2039',
    'firstName': "WILL",
    'lastName': "CABIÇO",
    'gender': '1',
    'country': "ANGOLA",
    'phone': "937574306",
    'email': "mikasamoreco@gmail.com",
    'type': '5956',
    'countryCode': '244'
  },
         ];

  function validateProfile(profile) {
    if (!profile) {
      alert("Erro: Perfil não encontrado");
      return;
    }
    fillProfileData(profile);
  }

  function fillProfileData(profile) {
    if (document.querySelector("#mat-input-7")) {
      fillField("#mat-input-7", profile.passportNumber);
      fillField("#mat-input-5", profile.firstName);
      fillField("#mat-input-6", profile.lastName);
      fillField("#dateOfBirth", profile.birthDate);
      fillField("#passportExpirtyDate", profile.passportExpiration);
      if (document.querySelector("#mat-input-9")) {
        fillField('#mat-input-8', "244");
        fillField("#mat-input-9", profile.phone);
      }
      if (document.querySelector("#mat-input-7")) {
        fillField("#mat-input-7", profile.passportNumber);
        fillField("#mat-input-10", profile.email);
      }
      selectOption("mat-select#mat-select-8", profile.country);
      selectOption('mat-select#mat-select-6', profile.gender === '1' ? "Male" : "Female");
    } else {
      fillField('#mat-input-0', profile.firstName);
      fillField("#mat-input-1", profile.lastName);
      fillField("#dateOfBirth", profile.birthDate);
      fillField("#passportExpirtyDate", profile.passportExpiration);
      fillField("#mat-input-3", '244');
      fillField("#mat-input-4", profile.phone);
      fillField("#mat-input-2", profile.passportNumber);
      fillField("#mat-input-5", profile.email);
      selectOption('mat-select#mat-select-2', profile.country);
      selectOption("mat-select#mat-select-0", profile.gender === '2' ? "Male" : "Female");
    }
  }

  function fillField(selector, value) {
    const field = document.querySelector(selector);
    if (field) {
      field.value = value;
      const inputEvent = new Event("input", {
        'bubbles': true
      });
      field.dispatchEvent(inputEvent);
      const changeEvent = new Event("change", {
        'bubbles': true
      });
      field.dispatchEvent(changeEvent);
    }
  }

  function selectOption(selector, value) {
    const dropdown = document.querySelector(selector);
    if (dropdown) {
      dropdown.click();
      setTimeout(() => {
        const options = document.querySelectorAll("mat-option");
        options.forEach(option => {
          if (option.textContent.trim() === value) {
            option.click();
            const changeEvent = new Event('change', {
              'bubbles': true
            });
            dropdown.dispatchEvent(changeEvent);
          }
        });
      }, 300);
    }
  }

  function clientSelection(event) {
    const selectedIndex = event.target.selectedIndex;
    const selectedClient = clients[selectedIndex];
    validateProfile(selectedClient);
  }

  function createClientSelector(element) {
    var select = document.createElement("select");
    select.className = 'custom-select';
    select.addEventListener("change", clientSelection);
    var options = clients.map((client, index) => {
      var option = document.createElement("option");
      let typeLabel = '';
      if (client.type == '0x1743') {
        typeLabel = 'SG';
      } else if (client.type == '0x1744') {
        typeLabel = '';
      } else {
        typeLabel = '';
      }
      option.text = client.firstName + "  " + client.lastName + " | " + typeLabel;
      option.value = index;
      return option;
    });
    options.forEach(option => select.add(option));
    let defaultOption = document.createElement("option");
    defaultOption.text = "Select Cliente";
    defaultOption.value = 0;
    select.add(defaultOption);
    element.appendChild(select);
  }

  function addCustomStyles() {
    var style = document.createElement("style");
    style.type = 'text/css';
    style.innerHTML = ".custom-select {\n width: 80%;\n  padding: 2px;\n font-size: 24px;\n font-family: Arial, sans-serif;\n border: 6px solid #ccc;\n border-radius: 6px;\n background-color: Pink;\n appearance: none; /* Remove o estilo padrão */\n background-repeat: no-repeat;\n background-position: right 10px center;\n  background-size: 10px;\n    cursor: pointer;\n  transition: border-color 0.3s ease, box-shadow 0.3s ease;\n      }\n\n      .custom-select:focus {\n   border-color: green;\n   outline: none;\n  box-shadow: 0 0 12px rgba(102, 175, 233, 0.6);\n   }\n    ";
    document.head.appendChild(style);
  }

  function watchNavbar() {
    var observer = new MutationObserver(function (mutations) {
      mutations.forEach(function (mutation) {
        mutation.addedNodes.forEach(function (node) {
          if (node.nodeType === 1 && node.matches(".collapse.navbar-collapse.navbarToggle")) {
            createClientSelector(node.querySelector(".navbar-nav.ml-auto.align-items-sm-center"));
          }
        });
      });
    });
    observer.observe(document.body, {
      'childList': true,
      'subtree': true
    });
  }

  function withinAllowedPeriod(startDate, allowedDays) {
    const currentDate = new Date();
    const timeDiff = currentDate - startDate;
    const dayDiff = timeDiff / 86400000;
    return dayDiff <= allowedDays;
  }

  function toggleSelectOption(selector, optionId) {
    var select = document.querySelector(selector);
    select.click();
    setTimeout(() => {
      const option = document.getElementById(optionId);
      if (option) {
        option.click();
      }
      select.click();
      const changeEvent = new Event('change', {
        'bubbles': true
      });
      select.dispatchEvent(changeEvent);
    }, 300);
  }

  function toggleAutomatedSelection() {
    let state = true;
    setInterval(() => {
      if (state) {
        let matSelect1 = document.querySelector("mat-select#mat-select-8");
        let matSelect2 = document.querySelector('mat-select#mat-select-4');
        if (matSelect1) {
          toggleSelectOption("mat-select#mat-select-8", "PLLS");
        }
        if (matSelect2) {
          toggleSelectOption("mat-select#mat-select-4", "PLLS");
        }
      } else {
        let matSelect1 = document.querySelector("mat-select#mat-select-8");
        let matSelect2 = document.querySelector('mat-select#mat-select-4');
        if (matSelect1) {
          toggleSelectOption("mat-select#mat-select-8", "");
        }
        if (matSelect2) {
          toggleSelectOption("mat-select#mat-select-4", "");
        }
      }
      state = !state;
    }, 70000);
  }

  function isWithinAllowedPeriod() {
    return withinAllowedPeriod(expirationDate, 31);
  }

  if (!isWithinAllowedPeriod()) {
    alert("O periodo de 365 dias expirou!");
  }

  watchNavbar();
  addCustomStyles();
  toggleAutomatedSelection();


})();