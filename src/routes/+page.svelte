<script lang="ts">
    import { onMount } from "svelte";
    import { fade } from "svelte/transition";

    let pageData: {
        text_items: {
            loc: number;
            text: string;
        }[];
        image_items: {
            loc: number;
            image: string;
        }[];
    }

    onMount(() => {
        addEventListener("scroll", () => {
            const header = document.getElementsByTagName("header")[0];
            if (!header) return;
            if (window.scrollY > 0) {
                header.style.backgroundColor = "#1b4584";
            } else {
                header.style.backgroundColor = "transparent";
            }
        });

        console.log(navigator.language);
        const userLang = navigator.language;
        const lang = userLang.split("-")[0];
        const flag = flags.filter(flag => flag.value == lang)[0];
        changeLanguage(flag);
    });

    function getText(loc: number, elementType: "text_items") {
        return pageData[elementType].filter(item => item.loc == loc)[0].text;
    }

    function getImage(loc: number, elementType: "image_items") {
        return pageData[elementType].filter(item => item.loc == loc)[0].image;
    }

    const flags = [
        {
            name: "English",
            value: "en",
            icon: "/flags/gb.svg",
        },
        {
            name: "Deutsch",
            value: "de",
            icon: "/flags/de.svg",
        },
        {
            name: "Français",
            value: "fr",
            icon: "/flags/fr.svg",
        },
        {
            name: "Nederlands",
            value: "nl",
            icon: "/flags/nl.svg",
        },
        {
            name: "Polski",
            value: "pl",
            icon: "/flags/pl.svg",
        },
    ];

    let selected = flags[0];

    function changeLanguage(flag: { name: string; value: string; icon: string; }) {
        console.log(flag);
        selected = flag;

        const options = {
            method: 'POST',
            body: `{"target_lang":"${selected.value.toLocaleUpperCase()}"}`,
            mode: "cors"
        };

        fetch('http://178.62.220.8:8080/content', options)
            .then(async response => response.json())
            .then(data => {
                pageData = data;
            })
            .catch(err => console.error(err));
    }
</script>

<header>
    <img src="./logo.svg" />

    <div id="select-container">
        <ul>
            <li tooltip={selected.name}>
                <button><img src={selected.icon} alt={selected.name} /></button>
            </li>
            {#each flags as flag}
                {#if selected.value !== flag.value}
                    <li tooltip={flag.name}>
                        <button on:click={() => {
                            changeLanguage(flag);
                        }}><img src={flag.icon} alt={flag.name} /></button>
                    </li>
                {/if}
            {/each}
        </ul>
    </div>
</header>

{#key pageData}
    {#if pageData}
        <body transition:fade={{
            duration: 300,
            delay: 0
        }}>
            <section id="main">
                <div id="info">
                    <h1>
                        {getText(1, "text_items")}
                    </h1>
                    <h2>{getText(2, "text_items")}<br>{getText(3, "text_items")}</h2>
                    <p>{getText(4, "text_items")}</p>
                    <button>{getText(5, "text_items")}</button>
                </div>
                <img src="/shutterstock_2109724295.png" alt="">
                <div id="filler"></div>
            </section>
            <section id="values">
                <img src={"http://178.62.220.8:8080/images/" + getImage(1000, "image_items")} alt="">
                <div id="info">
                    <h2>{@html getText(6, "text_items").replace(/\b(\w+)\b/, '<span style="color: #1b4584;">$1</span>')}</h2>
                    <span class="divider"></span>
                    <p>{getText(7, "text_items")}</p>
                    <p>{getText(8, "text_items")}</p>
                </div>
            </section>
        </body>
    {/if}
{/key}

<style lang="scss">
    @import url('https://fonts.googleapis.com/css2?family=Mukta:wght@400;700&family=Montserrat:wght@400;600;700&family=Outfit:wght@400;600;700&display=swap');

    :global(body) {
        margin: 0;
        font-family: Arial, sans-serif;
    }

    body {
        height: 120vh;
        max-width: 1024px;
        margin: 0 auto;
    }

    header {
        height: 50px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        position: fixed;
        z-index: 1000;
        transition: background-color 0.5s ease-in-out;
        width: 100%;
        max-width: 1024px;
        padding: 25px calc(50% - 512px + 30px);

        img {
            height: 100%;
        }

        #select-container {
            position: absolute;
            width: 50px;
            height: 40px;
            background: #ffffff;
            top: 25px;
            right: calc(50% - 512px + 30px);
            transform: translateX(-50%);
            border-radius: 10px;
            border: 0.5px solid rgba(202, 219, 226, 0.4);
            box-shadow: 0px 3px 6px rgba(79, 104, 113, 0.2);
            overflow: hidden;
            transition: height 0.2s ease-in-out,
            border-radius 0.2s ease-in-out,
            box-shadow 0.2s ease-in-out;

            &:hover {
                height: 202px;
                border-radius: 20px;
                box-shadow: 0px 7px 10px rgba(79, 104, 113, 0.3);
            }

            ul {
                list-style-type: none;
                position: relative;

                li {
                    opacity: 1;
                    transition: opacity 0.2s ease-in-out;
                    
                    &:first-child {
                        img {
                            cursor: default;
                        }
                    }
                    
                    img {
                        width: 25px;
                        height: 25px;

                        display: block;
                        left: 50%;
                        transform: translate(-103%, -40%);
                        margin-bottom: 8px;

                        border-radius: 50%;
                        border: 2px solid #ffffff;
                        box-shadow: 0px 0px 6px rgba(79, 104, 113, 0.3);

                        cursor: pointer;
                        transition: all 0.1s ease-in-out;
                        
                        &:hover {
                            box-shadow: 0px 5px 10px rgba(79, 104, 113, 0.3);
                            transform: translate(-103%, -40%) scale(1.08);
                        }
                    }

                    button {
                        background: none;
                        border: none;
                        cursor: pointer;
                        margin: 0;
                        padding: 0;
                    }
                }
            }
        }

        [tooltip] {
            position: relative;
            font-weight: bold;
        }

        [tooltip]::before,
        [tooltip]::after {
            text-transform: none;
            font-size: 12px;
            line-height: 1;
            user-select: none;
            pointer-events: none;
            position: absolute;
            display: none;
            opacity: 0;
        }

        [tooltip]::before {
            content: "";
            border: 5px solid transparent;
            z-index: 1001;
        }

        [tooltip]::after {
            content: attr(tooltip);

            font-family: Helvetica, sans-serif;
            text-align: center;

            white-space: nowrap;
            padding: 3px 0px;
            border-radius: 0.3ch;
            box-shadow: 0 1em 2em -0.5em rgba(0, 0, 0, 0.35);
            background: #465663;
            color: #fff;
            z-index: 1000;
        }

        [tooltip]:hover::before,
        [tooltip]:hover::after {
            display: block;
        }

        [tooltip=""]::before,
        [tooltip=""]::after {
            display: none !important;
        }

        [tooltip]:not([flow])::before {
            bottom: 50%;
            border-bottom-width: 0;
            border-top-color: #465663;
        }

        [tooltip]:not([flow])::after {
            bottom: calc(50% + 5px);
        }

        [tooltip]:not([flow])::before,
        [tooltip]:not([flow])::after {
            left: -15.5px;
            bottom: 40px;
            transform: translate(-50%, -0.5em);
        }

        @keyframes tooltips-vert {
            to {
                opacity: 0.9;
                transform: translate(-50%, 0);
            }
        }

        [tooltip]:not([flow]):hover::before,
        [tooltip]:not([flow]):hover::after {
            animation: tooltips-vert 300ms ease-out forwards;
        }
    }

    #main {
        display: flex;
        justify-content: space-between;
        align-items: center;
        color: #ffffff;
        padding-top: 180px;
        max-height: 410px;
        width: 100%;
        background-image: linear-gradient(245deg, #02010100 43%, #1b4584 43.07%);

        #info {
            width: 53%;
            padding-bottom: 64px;

            h1 {
                font-size: 35px;
                line-height: 1.2em;
                margin: 0;
                color: #eb5e25;
                margin-bottom: .6rem;
                font-family: "Mukta", Sans-serif;
            }

            h2 {
                font-size: 25px;
                font-weight: 600;
                margin: 0;
                margin-bottom: 30px;
                line-height: 1.2em;
                font-family: "Mukta", Sans-serif;
            }

            p {
                font-size: 1rem;
                margin-bottom: 20px;
                line-height: 29px;
                font-family: "Montserrat", Sans-serif
            }

            button {
                background-color: #eb5e25;
                color: #ffffff;
                padding: 8px 30px;
                line-height: 2rem;
                border: none;
                border-radius: 50px;
                font-weight: 400;
                font-size: 1rem;
                margin-top: 20px;
                cursor: pointer;
                transition: all 0.2s ease-in-out;
                font-family: "Outfit", Sans-serif;
                text-transform: uppercase;
                font-weight: 400;

                &:hover {
                    background-color: #ffffff;
                    color: #1b4584;
                }
            }
        }

        img {
            position: absolute;
            right: 0;
            top: 0;
            width: 53vw;
            height: 100%;
            max-height: 620px;
            z-index: -1;
            object-fit: cover;
        }

        #filler {
            position: absolute;
            left: 0;
            top: 0;
            height: 100%;
            max-height: 620px;
            width: 30%;
            z-index: -1;
            background-color: #1b4584;
        }
    }

    #values {
        display: flex;
        justify-content: space-around;
        align-items: center;

        #info {
            background-color: #ffffff;
            padding: 30px;
            padding-bottom: 64px;

            h2 {
                color: #eb5e25;
                font-size: 35px;
                font-weight: 600;
                margin: 0;
                line-height: 1.2em;
                font-family: "Mukta", Sans-serif;
            }

            p {
                font-size: 1rem;
                line-height: 29px;
                font-family: "Montserrat", Sans-serif
            }
        }

        img {
            width: 47%;
            height: 100%;
            object-fit: cover;
        }
    }

    section {
        padding: 30px;
    }

    .divider {
        display: block;
        width: 140px;
        height: 2px;
        background-color: #1b4584;
        margin-bottom: 30px;
    }
</style>
