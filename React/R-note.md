```html
<script type="text/babel">
  // React18以前
	ReactDom.render(<h2>Hello World</h2>,document.querySelector("#root"))
	// React18
	const root = ReactDOM.createRoot(document.querySelector('#root'))
  root.render(<h2>Hello Word</h2>)
</script>
```

