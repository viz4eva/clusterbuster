<script>
    export let message;
</script>

<div class="spinner-container">
    <svg class="dot" width="300" height="60">
        <circle class="spinner" cx="100" cy="10" r="3" fill="#5e89a8">
            <animate
            id="first"
                attributeName="r"
                values="3;10;3"
                dur="2"
                repeatCount = "indefinite"
            />
        </circle>
        <text x="10" y="50">{message}</text>
    </svg>
</div>

<style>
    .spinner-container {
        display: flex;
        justify-content: center;
        margin: auto;
        padding-top: 20px;
    }  
</style>
