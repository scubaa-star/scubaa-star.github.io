<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>r/TravisandTaylor Variant Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 font-sans">
    <!-- Header -->
    <header class="bg-purple-600 text-white p-4">
        <nav class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl font-bold">r/TravisandTaylor Variant Tracker</h1>
            <ul class="flex space-x-4">
                <li><a href="#home" class="hover:underline">Home</a></li>
                <li><a href="#albums" class="hover:underline">Albums</a></li>
                <li><a href="#contact" class="hover:underline">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto my-8 px-4">
        <section id="home" class="mb-12">
            <h2 class="text-3xl font-semibold mb-4">Welcome to the Variant Tracker!</h2>
            <p class="text-lg">This website tracks different versions of Taylor Swift's albums. Data is stored in a JSON file that you can manually update. Scroll down to view albums and their versions.</p>
            <p class="text-lg mt-2">To add or edit albums, modify the <code class="bg-gray-200 px-1 rounded">albums.json</code> file in the same directory as this page.</p>
            <p class="text-lg mt-2"><strong>Important:</strong> To run this website in VS Code, install the <a href="https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer" class="text-blue-600 hover:underline">Live Server</a> extension, right-click <code class="bg-gray-200 px-1 rounded">index.html</code>, and select "Open with Live Server" to serve it at <a href="http://localhost:5500" class="text-blue-600 hover:underline">http://localhost:5500</a>. Opening <code class="bg-gray-200 px-1 rounded">index.html</code> directly in a browser (file://) will cause errors.</p>
        </section>

        <section id="albums" class="mb-12">
            <h2 class="text-3xl font-semibold mb-4">Albums and Versions</h2>
            <div id="album-list" class="space-y-8">
                <p class="text-lg">Loading albums...</p>
            </div>
        </section>

        <section id="contact">
            <h2 class="text-3xl font-semibold mb-4">Contact Us</h2>
            <div class="max-w-md">
                <div class="mb-4">
                    <label for="name" class="block text-lg mb-2">Name</label>
                    <input type="text" id="name" class="w-full p-2 border rounded" placeholder="Your name">
                </div>
                <div class="mb-4">
                    <label for="message" class="block text-lg mb-2">Message</label>
                    <textarea id="message" class="w-full p-2 border rounded" rows="4" placeholder="Your message"></textarea>
                </div>
                <button onclick="submitForm()" class="bg-purple-600 text-white px-4 py-2 rounded hover:bg-purple-700">Send</button>
            </div>
        </section>
    </main>

    <!-- Footer -->
    <footer class="bg-gray-800 text-white p-4 text-center">
        <p>&copy; 2025 r/TravisandTaylor Variant Tracker. All rights reserved.</p>
    </footer>

    <script>
        async function loadAlbums() {
            const albumList = document.getElementById('album-list');

            try {
                // Fetch the JSON file
                const response = await fetch('albums.json');
                if (!response.ok) {
                    if (response.status === 404) {
                        throw new Error('albums.json not found. Ensure it is in the same directory as index.html.');
                    } else {
                        throw new Error(`Failed to load albums.json: ${response.status} ${response.statusText}`);
                    }
                }

                let data;
                try {
                    data = await response.json();
                } catch (e) {
                    throw new Error('Invalid JSON in albums.json. Please check the file for syntax errors.');
                }

                if (!data.albums || !Array.isArray(data.albums) || data.albums.length === 0) {
                    albumList.innerHTML = '<p class="text-yellow-600">No albums found in albums.json. Add albums to the file.</p>';
                    return;
                }

                albumList.innerHTML = ''; // Clear loading message

                data.albums.forEach(album => {
                    const albumDiv = document.createElement('div');
                    albumDiv.className = 'bg-white p-4 rounded shadow';

                    const title = document.createElement('h3');
                    title.className = 'text-2xl font-bold mb-2';
                    title.textContent = `${album.title || 'Unknown Album'} (${album.year || 'N/A'})`;
                    albumDiv.appendChild(title);

                    if (album.versions && Array.isArray(album.versions) && album.versions.length > 0) {
                        const table = document.createElement('table');
                        table.className = 'w-full border-collapse border border-gray-300';
                        table.innerHTML = `
                            <thead>
                                <tr class="bg-gray-200">
                                    <th class="border p-2">Year</th>
                                    <th class="border p-2">Country</th>
                                    <th class="border p-2">Format</th>
                                    <th class="border p-2">Label</th>
                                    <th class="border p-2">Notes</th>
                                </tr>
                            </thead>
                            <tbody></tbody>
                        `;
                        const tbody = table.querySelector('tbody');

                        album.versions.forEach(version => {
                            const row = document.createElement('tr');
                            row.innerHTML = `
                                <td class="border p-2">${version.year || 'N/A'}</td>
                                <td class="border p-2">${version.country || 'N/A'}</td>
                                <td class="border p-2">${version.format || 'N/A'}</td>
                                <td class="border p-2">${version.label || 'N/A'}</td>
                                <td class="border p-2">${version.notes || ''}</td>
                            `;
                            tbody.appendChild(row);
                        });

                        albumDiv.appendChild(table);
                    } else {
                        const p = document.createElement('p');
                        p.textContent = 'No versions found.';
                        albumDiv.appendChild(p);
                    }

                    albumList.appendChild(albumDiv);
                });
            } catch (error) {
                console.error('Error loading albums:', error);
                albumList.innerHTML = `<p class="text-red-600">Error loading data: ${error.message}. Ensure albums.json exists in the same directory and is served via a web server (e.g., <a href="http://localhost:5500" class="text-blue-600 hover:underline">http://localhost:5500</a>).</p>`;
            }
        }

        // Load albums on page load
        window.onload = loadAlbums;

        function submitForm() {
            const name = document.getElementById('name').value;
            const message = document.getElementById('message').value;
            if (name && message) {
                alert(`Thank you, ${name}! Your message: "${message}" has been received.`);
                document.getElementById('name').value = '';
                document.getElementById('message').value = '';
            } else {
                alert('Please fill out both fields.');
            }
        }
    </script>
</body>
</html>
