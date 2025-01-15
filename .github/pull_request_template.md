// ==UserScript==
// @name         Em Viagens Travel
// @namespace    http://tampermonkey.net/
// @version      1.7
// @description  Script para preencher automaticamente formulários com 6 perfis diferentes usando botões de perfis na lateral da página.
// @author       laurindo
// @match        https://visa.vfsglobal.com/*
// @grant        none
// ==/UserScript==

(function () {
    'use strict';

    // Perfis de preenchimento
    const perfis = [
        {
            nome: "Nacional",
            primeiroNome: "ANTONIO",
            ultimoNome: "MICOLO",
            dataNascimento: "13/06/1990",
            numeroPassaporte: "N3420081",
            dataExpiracaoPassaporte: "02/09/2039",
            telefone: "937574306",
            email: "mikasamoreco@gmail.com",
            codigoPais: "244"
        },
        {
            nome: "Nacional",
            primeiroNome: "JORGE",
            ultimoNome: "CASIMIRO",
            dataNascimento: "14/06/2003",
            numeroPassaporte: "N3382629",
            dataExpiracaoPassaporte: "01/08/2034",
            telefone: "937574306",
            email: "mikasamoreco@gmail.com",
            codigoPais: "244"
        },
        {
            nome: "prioridade",
            primeiroNome: "WILL",
            ultimoNome: "CABIÇO",
            dataNascimento: "01/08/1982",
            numeroPassaporte: "N3494361",
            dataExpiracaoPassaporte: "07/11/2039",
            telefone: "937574306",
            email: "mikasamoreco@gmail.com",
            codigoPais: "244"
        },
        {codigoPais: "244"

        }
    ];

    // Função para preencher o formulário com base no perfil selecionado
    function preencherFormulario(perfil) {
        const mapCampos = {
            primeiroNome: "input[placeholder='Enter your first name']",
            ultimoNome: "input[placeholder='Please enter last name.']",
            dataNascimento: "input[placeholder='Please select the date']",
            numeroPassaporte: "input[placeholder='Enter passport number']",
            dataExpiracaoPassaporte: "#passportExpirtyDate",
            codigoPais: "input[placeholder='44']",
            telefone: "input[placeholder='012345648382']",
            email: "input[placeholder='Enter Email Address']",
        };

        for (const campo in mapCampos) {
            const input = document.querySelector(mapCampos[campo]);
            if (input && perfil[campo]) {
                input.value = perfil[campo];
                input.dispatchEvent(new Event('input', { bubbles: true }));
            }
        }

        alert(`Campos preenchidos com sucesso para ${perfil.nome}!`);
    }

    // Adicionar botões de perfil na lateral
    function addProfileButtons() {
        const sidebar = document.createElement('div');
        sidebar.style.cssText = `
            position: fixed;
            top: 10%;
            right: 0;
            z-index: 9999;
            background: rgba(255, 255, 255, 0.95);
            width: 200px;
            padding: 10px;
            border-radius: 5px 0 0 5px;
            box-shadow: -3px 0 5px rgba(0, 0, 0, 0.2);
        `;
        sidebar.innerHTML = `<h3 style="text-align: center;">Escolher Perfil</h3>`;
        document.body.appendChild(sidebar);

        perfis.forEach((perfil, index) => {
            const button = document.createElement('button');
            button.textContent = perfil.nome;
            button.style.cssText = `
                display: block;
                margin: 5px auto;
                padding: 8px;
                width: 90%;
                background: #b94c19;
                color: white;
                border: none;
                border-radius: 5px;
                cursor: pointer;
            `;
            button.addEventListener('click', () => preencherFormulario(perfil));
            sidebar.appendChild(button);
        });
    }

    // Executar ao carregar a página
    window.addEventListener('load', addProfileButtons);
})();