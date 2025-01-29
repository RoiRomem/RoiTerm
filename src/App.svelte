<script lang="ts">
  import { onMount, tick } from 'svelte';

  let terminalDiv: HTMLDivElement;

  const scrollToBottom = async () => {
    await tick(); // Wait for DOM update
    if (terminalDiv) {
      terminalDiv.scrollTop = terminalDiv.scrollHeight;
    }
  };

  const myProjects = {
    "Ascii Language": "https://github.com/RoiRomem/Ascii-Language",
    "Frankenstien Game Framework": "https://github.com/RoiRomem/Frankenstien-Game-Framework",
    "Telnet Chat Server": "https://github.com/RoiRomem/telnet-chat-server",
    "Go Login System": "https://github.com/RoiRomem/Go-Login-System",
    "more": "https://github.com/RoiRomem/",
  };

  const initPrompt: string = 'RoiTerm'

  let input: string = '';
  let output: string[] = [
    initPrompt, // Initial prompt text
  ];

  function handleKeydown(event: KeyboardEvent): void {
    if (event.key === 'Enter') {
      output = [...output, `> $${input}`];
      const command = input.endsWith('.sh') ? input.toLowerCase().slice(0, input.lastIndexOf('.sh')) : input.toLowerCase();
      input = '';

      switch (command) {
        case 'about':
          output = [
            ...output,
            `Hey, I'm Roi Romem,`,
            `a 15 y/o sophomore from Motza Illit.`,
            `Fun fact about me: I've been coding since I was 8!`,
            `If you want to see my projects, use 'projects'`,
          ];
          break;
        case 'projects':
          const projectLinks = Object.entries(myProjects)
                  .map(
                          ([project, url]) =>
                                  `<a href="${url}" target="_blank" class="project-link">${project}</a>`
                  )
                  .join('<br />');
          output = [...output, projectLinks];
          break;
        case 'coding':
          output = [...output, 'coding = [".c", ".cs", ".go", ".java", ".py", ".js", ".ts", ".css", ".html"]'];
          break;
        case 'frameworks':
          output = [...output, 'frameworks = ["svelte", "react"]']
          break;
        case 'learning':
          output = [...output, `At the moment I'm learning System's level programing languages and networks`]
          break;
        case 'clear':
          output = [initPrompt];
          break;
        case 'ls':
          output = [...output,
            'about.sh',
            'projects.sh',
            'coding.sh',
            'frameworks.sh',
            'learning.sh',
            'clear',
            'echo'
          ];
          break;
        case 'help':
          output = [...output, "use 'ls'"];
          break;
        default:
          if (!command.startsWith('echo')) {
            output = [...output, `Unknown command: ${command}`];
          } else {
            output = [...output, command.replace(/^echo\s*/i, '')];
          }
      }
      scrollToBottom();
    }
  }

  // Auto-scroll when component mounts
  onMount(() => {
    scrollToBottom();
  });

  // Watch for changes in output array
  $: {
    output;
    scrollToBottom();
  }
</script>

<style lang="scss">
  .terminal {
    background-color: #2e2e2e;
    color: #e8e8e8;
    font-family: monospace;
    padding: 20px;
    border-radius: 5px;
    border: 2px solid black;
    max-width: 80%;
    margin: 50px auto;
    height: 400px; /* Fixed height for scrolling */
    overflow-y: auto;
    scroll-behavior: smooth;
  }

  .terminal div {
    white-space: pre-wrap;
  }

  .command {
    color: #8aff80;
  }

  :global(a) {
    color: #8aff80;
    text-decoration: none;
  }

  :global(a:hover) {
    text-decoration: underline;
  }

  .input-wrapper {
    display: flex;
    align-items: center;
  }

  .terminal input {
    background: transparent;
    border: none;
    color: #8aff80;
    font-family: monospace;
    width: 100%;
    outline: none;
    padding-left: 10px;
  }

  .dollar-sign {
    color: #8aff80;
    padding-right: 5px;
  }
</style>

<div class="terminal" bind:this={terminalDiv}>
  <div>
    {#each output as line}
      <div class="command">{@html line}</div>
    {/each}
  </div>

  <div class="input-wrapper">
    <span class="dollar-sign">$</span>
    <input
            bind:value={input}
            on:keydown={handleKeydown}
            placeholder=""
    />
  </div>
</div>
