<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ethiopian Meme Templates</title>
    <!-- Use Tailwind CSS for a responsive and modern design -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        .meme-card {
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        .meme-card:hover {
            transform: translateY(-5px) scale(1.02);
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
        }
        .modal-overlay {
            background-color: rgba(0, 0, 0, 0.75);
        }
        .modal-content {
            max-width: 90%;
            max-height: 90vh;
        }
        .upload-form-container {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease-out;
        }
        .upload-form-container.open {
            max-height: 500px; /* Adjust as needed, should be larger than form content */
        }
    </style>
</head>
<body class="bg-gray-100 text-gray-800">

    <!-- Firebase SDK Imports -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, onSnapshot, addDoc, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the Canvas environment
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);

        // Export globals for use in the main script
        window.firebase = {
            db,
            auth,
            appId,
            initialAuthToken,
            signInWithCustomToken,
            signInAnonymously,
            onAuthStateChanged,
            collection,
            onSnapshot,
            addDoc,
            serverTimestamp
        };
    </script>

    <!-- Main container -->
    <div class="container mx-auto p-4 sm:p-8">

        <!-- Header and Search Section -->
        <header class="bg-white rounded-lg shadow-lg p-6 mb-8 flex flex-col md:flex-row items-center justify-between">
            <h1 class="text-3xl sm:text-4xl font-bold text-gray-900 mb-4 md:mb-0">Ethiopian Meme Templates</h1>
            <div class="flex flex-col sm:flex-row space-y-4 sm:space-y-0 sm:space-x-4 w-full md:w-auto">
                <!-- User ID display -->
                <div id="user-info" class="flex items-center space-x-2 text-sm text-gray-500">
                    <span id="user-id">Signing in...</span>
                </div>
                <!-- Search Input -->
                <div class="relative w-full">
                    <input type="text" id="search-input" placeholder="Search for memes..."
                           class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-full focus:outline-none focus:ring-2 focus:ring-orange-500 transition-all duration-200">
                    <svg class="absolute left-3 top-1/2 transform -translate-y-1/2 w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                    </svg>
                </div>
                <!-- Filter and Upload Buttons -->
                <div class="flex space-x-2">
                    <button id="filter-all" class="px-4 py-2 text-sm font-semibold text-white bg-orange-600 hover:bg-orange-700 rounded-full transition-colors duration-200">
                        All
                    </button>
                    <button id="filter-image" class="px-4 py-2 text-sm font-semibold text-gray-700 bg-gray-200 hover:bg-orange-300 rounded-full transition-colors duration-200">
                        Images
                    </button>
                    <button id="filter-video" class="px-4 py-2 text-sm font-semibold text-gray-700 bg-gray-200 hover:bg-orange-300 rounded-full transition-colors duration-200">
                        Videos
                    </button>
                    <button id="toggle-upload-form" class="px-4 py-2 text-sm font-semibold text-white bg-blue-600 hover:bg-blue-700 rounded-full transition-colors duration-200">
                        Upload Meme
                    </button>
                </div>
            </div>
        </header>

        <!-- Upload Form Section (hidden by default) -->
        <div id="upload-form-container" class="upload-form-container bg-white rounded-lg shadow-lg p-6 mb-8">
            <h2 class="text-2xl font-bold mb-4">Upload a New Meme Template</h2>
            <form id="upload-form" class="space-y-4">
                <div>
                    <label for="meme-name" class="block text-sm font-medium text-gray-700">Meme Name</label>
                    <input type="text" id="meme-name" required
                           class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-orange-500 focus:ring-orange-500 sm:text-sm p-2">
                </div>
                <div>
                    <label for="meme-type" class="block text-sm font-medium text-gray-700">Media Type</label>
                    <select id="meme-type" required
                            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-orange-500 focus:ring-orange-500 sm:text-sm p-2">
                        <option value="image">Image</option>
                        <option value="video">Video</option>
                    </select>
                </div>
                <div>
                    <label for="meme-url" class="block text-sm font-medium text-gray-700">Media URL (Image or Video)</label>
                    <input type="url" id="meme-url" required
                           class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-orange-500 focus:ring-orange-500 sm:text-sm p-2"
                           placeholder="e.g., https://example.com/image.jpg">
                </div>
                <div>
                    <label for="meme-tags" class="block text-sm font-medium text-gray-700">Tags (comma separated)</label>
                    <input type="text" id="meme-tags"
                           class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-orange-500 focus:ring-orange-500 sm:text-sm p-2"
                           placeholder="e.g., food, habesha, funny">
                </div>
                <div class="flex justify-end">
                    <button type="submit" id="submit-btn" class="px-6 py-2 text-sm font-semibold text-white bg-blue-600 hover:bg-blue-700 rounded-full transition-colors duration-200">
                        Upload
                    </button>
                </div>
            </form>
            <div id="upload-status" class="mt-4 text-center text-sm"></div>
        </div>


        <!-- Meme Gallery Section -->
        <main id="meme-gallery" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
            <!-- Meme cards will be dynamically inserted here by JavaScript -->
            <div class="col-span-full text-center text-gray-500 text-xl animate-pulse" id="loading-message">
                Loading meme templates...
            </div>
        </main>

        <!-- No results message -->
        <div id="no-results" class="text-center text-gray-500 font-semibold text-xl mt-12 hidden">
            No templates found. Try a different search.
        </div>

    </div>

    <!-- Modal for displaying a single meme -->
    <div id="meme-modal" class="fixed inset-0 z-50 hidden flex items-center justify-center modal-overlay transition-opacity duration-300 opacity-0">
        <div class="modal-content bg-white p-4 sm:p-6 rounded-lg shadow-xl relative transform transition-transform duration-300 scale-95">
            <!-- Close button -->
            <button id="modal-close-btn" class="absolute top-2 right-2 text-gray-500 hover:text-gray-900 transition-colors duration-200">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
            </button>
            <div id="modal-body">
                <!-- Meme content will be inserted here -->
            </div>
            <h3 id="modal-title" class="text-xl font-bold mt-4"></h3>
            <p id="modal-tags" class="text-sm text-gray-500"></p>
        </div>
    </div>

    <!-- JavaScript for dynamic content and interactivity -->
    <script type="module">
        // Ensure Firebase is loaded before running the main script
        window.addEventListener('load', async () => {
            const {
                db, auth, appId, initialAuthToken, signInWithCustomToken, signInAnonymously, onAuthStateChanged, collection, onSnapshot, addDoc, serverTimestamp
            } = window.firebase;

            // --- DOM Elements ---
            const memeGallery = document.getElementById('meme-gallery');
            const searchInput = document.getElementById('search-input');
            const filterButtons = {
                all: document.getElementById('filter-all'),
                image: document.getElementById('filter-image'),
                video: document.getElementById('filter-video')
            };
            const noResultsMessage = document.getElementById('no-results');
            const loadingMessage = document.getElementById('loading-message');
            const modal = document.getElementById('meme-modal');
            const modalBody = document.getElementById('modal-body');
            const modalTitle = document.getElementById('modal-title');
            const modalTags = document.getElementById('modal-tags');
            const modalCloseBtn = document.getElementById('modal-close-btn');

            // Upload form elements
            const toggleUploadFormBtn = document.getElementById('toggle-upload-form');
            const uploadFormContainer = document.getElementById('upload-form-container');
            const uploadForm = document.getElementById('upload-form');
            const memeNameInput = document.getElementById('meme-name');
            const memeTypeInput = document.getElementById('meme-type');
            const memeUrlInput = document.getElementById('meme-url');
            const memeTagsInput = document.getElementById('meme-tags');
            const submitBtn = document.getElementById('submit-btn');
            const uploadStatus = document.getElementById('upload-status');
            const userIdDisplay = document.getElementById('user-id');

            // --- State Management ---
            let currentFilter = 'all';
            let allMemes = [];
            let userId = 'unknown';

            // --- Firebase Authentication Setup ---
            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    userId = user.uid;
                    userIdDisplay.textContent = `User ID: ${userId}`;
                    // Start listening for memes after auth is ready
                    setupMemeListener();
                } else {
                    try {
                        if (initialAuthToken) {
                            await signInWithCustomToken(auth, initialAuthToken);
                        } else {
                            await signInAnonymously(auth);
                        }
                    } catch (error) {
                        console.error("Firebase auth error:", error);
                        userIdDisplay.textContent = "Error signing in.";
                        // Still try to load data, but it might fail depending on rules
                        setupMemeListener();
                    }
                }
            });

            // --- Real-time Data Listener from Firestore ---
            function setupMemeListener() {
                const collectionPath = `artifacts/${appId}/public/data/memes`;
                const q = collection(db, collectionPath);
                onSnapshot(q, (querySnapshot) => {
                    allMemes = [];
                    querySnapshot.forEach((doc) => {
                        allMemes.push({ id: doc.id, ...doc.data() });
                    });
                    loadingMessage.classList.add('hidden');
                    filterMemes();
                }, (error) => {
                    console.error("Error fetching memes:", error);
                    loadingMessage.textContent = "Failed to load memes. Please try again later.";
                });
            }

            // --- Functions ---
            // Renders the meme cards to the gallery
            function renderMemes(memesToRender) {
                memeGallery.innerHTML = '';
                if (memesToRender.length === 0) {
                    noResultsMessage.classList.remove('hidden');
                } else {
                    noResultsMessage.classList.add('hidden');
                }

                memesToRender.forEach(meme => {
                    const memeCard = document.createElement('div');
                    memeCard.className = 'meme-card bg-white rounded-lg shadow-md overflow-hidden transform hover:scale-105 transition-transform duration-300';
                    memeCard.dataset.id = meme.id;

                    const title = meme.name || 'Untitled Meme';
                    const tags = meme.tags && meme.tags.length > 0 ? meme.tags.map(tag => `#${tag}`).join(' ') : '';
                    const thumbnailUrl = meme.type === 'video' ? 'https://placehold.co/400x300/1E293B/FFFFFF?text=Video+Preview' : meme.url;

                    memeCard.innerHTML = `
                        <div class="relative w-full h-48 bg-gray-800 flex items-center justify-center overflow-hidden">
                            ${meme.type === 'image' ?
                                `<img src="${meme.url}" alt="${title}" class="w-full h-48 object-cover">` :
                                `<video src="${meme.url}" class="w-full h-full object-cover" controls preload="none" muted></video>`
                            }
                            ${meme.type === 'video' ?
                                `<div class="absolute inset-0 flex items-center justify-center bg-black bg-opacity-50 text-white text-3xl">
                                    <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.752 11.127l-3.327-2.218A1 1 0 0010 9.761v4.478a1 1 0 001.425.891l3.327-2.218a1 1 0 000-1.782z"></path>
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                                    </svg>
                                </div>` : ''
                            }
                        </div>
                        <div class="p-4">
                            <h2 class="text-lg font-semibold truncate">${title}</h2>
                            <p class="text-sm text-gray-500 truncate">${tags}</p>
                        </div>
                    `;

                    memeCard.addEventListener('click', () => showModal(meme));
                    memeGallery.appendChild(memeCard);
                });
            }

            // Filters memes based on the search query and current filter type
            function filterMemes() {
                const query = searchInput.value.toLowerCase();
                const filteredMemes = allMemes.filter(meme => {
                    const nameMatch = meme.name ? meme.name.toLowerCase().includes(query) : false;
                    const tagsMatch = meme.tags ? meme.tags.some(tag => tag.toLowerCase().includes(query)) : false;
                    const matchesSearch = nameMatch || tagsMatch;
                    const matchesFilter = currentFilter === 'all' || meme.type === currentFilter;
                    return matchesSearch && matchesFilter;
                });
                renderMemes(filteredMemes);
            }

            // Updates the active filter button and triggers a new filter
            function updateFilterButton(activeButton) {
                Object.values(filterButtons).forEach(button => {
                    button.classList.remove('bg-orange-600', 'text-white', 'hover:bg-orange-700');
                    button.classList.add('bg-gray-200', 'text-gray-700', 'hover:bg-orange-300');
                });
                activeButton.classList.remove('bg-gray-200', 'text-gray-700', 'hover:bg-orange-300');
                activeButton.classList.add('bg-orange-600', 'text-white', 'hover:bg-orange-700');
            }

            // Shows the modal with the selected meme's content
            function showModal(meme) {
                modalBody.innerHTML = ''; // Clear previous content
                if (meme.type === 'image') {
                    modalBody.innerHTML = `<img src="${meme.url}" alt="${meme.name}" class="w-full h-auto object-contain rounded-lg">`;
                } else if (meme.type === 'video') {
                    modalBody.innerHTML = `<video src="${meme.url}" class="w-full h-auto object-contain rounded-lg" controls autoplay></video>`;
                }
                modalTitle.textContent = meme.name || 'Untitled Meme';
                modalTags.textContent = meme.tags && meme.tags.length > 0 ? meme.tags.map(tag => `#${tag}`).join(' ') : '';

                modal.classList.remove('hidden', 'opacity-0');
                modal.classList.add('flex', 'opacity-100');
                setTimeout(() => {
                    modal.querySelector('.modal-content').classList.remove('scale-95');
                    modal.querySelector('.modal-content').classList.add('scale-100');
                }, 50);
            }

            // Closes the modal
            function closeModal() {
                // Pause any playing video before closing
                const video = modalBody.querySelector('video');
                if (video) {
                    video.pause();
                }
                modal.querySelector('.modal-content').classList.remove('scale-100');
                modal.querySelector('.modal-content').classList.add('scale-95');
                setTimeout(() => {
                    modal.classList.remove('flex', 'opacity-100');
                    modal.classList.add('hidden', 'opacity-0');
                }, 300);
            }

            // --- Event Listeners ---
            // Search input listener
            searchInput.addEventListener('input', filterMemes);

            // Filter button listeners
            filterButtons.all.addEventListener('click', () => {
                currentFilter = 'all';
                updateFilterButton(filterButtons.all);
                filterMemes();
            });
            filterButtons.image.addEventListener('click', () => {
                currentFilter = 'image';
                updateFilterButton(filterButtons.image);
                filterMemes();
            });
            filterButtons.video.addEventListener('click', () => {
                currentFilter = 'video';
                updateFilterButton(filterButtons.video);
                filterMemes();
            });

            // Close modal listeners
            modalCloseBtn.addEventListener('click', closeModal);
            modal.addEventListener('click', (e) => {
                if (e.target === modal) {
                    closeModal();
                }
            });

            // Toggle upload form visibility
            toggleUploadFormBtn.addEventListener('click', () => {
                uploadFormContainer.classList.toggle('open');
            });

            // Handle upload form submission
            uploadForm.addEventListener('submit', async (e) => {
                e.preventDefault();

                const name = memeNameInput.value;
                const type = memeTypeInput.value;
                const url = memeUrlInput.value;
                const tags = memeTagsInput.value.split(',').map(tag => tag.trim()).filter(tag => tag.length > 0);

                if (!name || !type || !url) {
                    uploadStatus.textContent = 'Please fill in all required fields.';
                    uploadStatus.className = 'mt-4 text-center text-sm text-red-500';
                    return;
                }

                submitBtn.disabled = true;
                submitBtn.textContent = 'Uploading...';
                uploadStatus.textContent = '';

                try {
                    const memeData = {
                        name,
                        type,
                        url,
                        tags,
                        createdAt: serverTimestamp(),
                        createdBy: userId
                    };
                    const collectionPath = `artifacts/${appId}/public/data/memes`;
                    await addDoc(collection(db, collectionPath), memeData);
                    
                    uploadStatus.textContent = 'Meme uploaded successfully!';
                    uploadStatus.className = 'mt-4 text-center text-sm text-green-500';
                    uploadForm.reset();
                    // Keep the form open for more uploads
                    // uploadFormContainer.classList.remove('open');
                } catch (error) {
                    console.error("Error uploading meme: ", error);
                    uploadStatus.textContent = 'Failed to upload meme. Please try again.';
                    uploadStatus.className = 'mt-4 text-center text-sm text-red-500';
                } finally {
                    submitBtn.disabled = false;
                    submitBtn.textContent = 'Upload';
                }
            });

        });
    </script>
</body>
</html>
