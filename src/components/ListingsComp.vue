<template>
  <div class="listings-container">
    <header class="app-header">
      <div class="header-content">
        <h1>Zee's property Rentals</h1>
        <p class="subtitle">Short-term rentals in Cape Town</p>
      </div>
      <div class="stats-badge">
        <span>Available: <strong>{{ availableCount }}</strong> / {{ properties.length }}</span>
      </div>
    </header>

    <section class="controls-section">
      <div class="search-wrapper">
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Search by title or location (e.g., Camps Bay)..."
          class="search-input"
        />
        <button v-if="searchQuery" @click="searchQuery = ''" class="clear-btn">&times;</button>
      </div>

      <div class="sort-wrapper">
        <label for="priceSort">Sort by Price:</label>
        <select id="priceSort" v-model="sortBy" class="sort-select">
          <option value="default">Featured</option>
          <option value="low-high">Low to High</option>
          <option value="high-low">High to Low</option>
        </select>
      </div>
    </section>

    <main class="grid-container">
      <div v-if="filteredAndSortedProperties.length === 0" class="no-results">
        <p>No properties match your criteria. Try adjusting your search.</p>
      </div>

      <div 
        v-else 
        v-for="property in filteredAndSortedProperties" 
        :key="property.id" 
        class="property-card"
        :class="{ 'is-unavailable': !property.isAvailable }"
      >
        <div class="image-wrapper">
          <img :src="property.image" :alt="property.title" class="property-image" />
          <div v-if="!property.isAvailable" class="unavailable-ribbon">
            Not Available
          </div>
          <button 
            @click="toggleBookmark(property.id)" 
            class="bookmark-btn"
            :class="{ 'is-bookmarked': bookmarks.includes(property.id) }"
            title="Toggle Bookmark"
          >
            ★
          </button>
        </div>

        <div class="card-content">
          <div class="card-meta">
            <span class="property-type">{{ property.type }}</span>
            <span class="property-location">📍 {{ property.location }}</span>
          </div>
          
          <h3 class="property-title">{{ property.title }}</h3>
          
          <div class="card-footer">
            <span class="property-price">R {{ property.price.toLocaleString() }} <small>/ night</small></span>
            <button 
              class="contact-btn" 
              :disabled="!property.isAvailable"
              @click="contactAgent(property)"
            >
              Contact Agent
            </button>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'ListingsComp',
  data() {
    return {
      searchQuery: '',
      sortBy: 'default',
      
      properties: [
        {
          id: 1,
          title: 'Luxury Camps Bay Villa',
          location: 'Camps Bay',
          price: 4500,
          type: 'Villa',
          isAvailable: true,
          image: 'https://images.unsplash.com/photo-1512917774080-9991f1c4c750?auto=format&fit=box&w=600&q=80'
        },
        {
          id: 2,
          title: 'Modern Waterfront Apartment',
          location: 'V&A Waterfront',
          price: 3200,
          type: 'Apartment',
          isAvailable: true,
          image: 'https://images.unsplash.com/photo-1545324418-cc1a3fa10c00?auto=format&fit=box&w=600&q=80'
        },
        {
          id: 3,
          title: 'Cozy Green Point Studio',
          location: 'Green Point',
          price: 1500,
          type: 'Studio',
          isAvailable: false,
          image: 'https://images.unsplash.com/photo-1502672260266-1c1ef2d93688?auto=format&fit=box&w=600&q=80'
        },
        {
          id: 4,
          title: 'Tranquil Noordhoek Cottage',
          location: 'Noordhoek',
          price: 2200,
          type: 'Cottage',
          isAvailable: true,
          image: 'https://images.unsplash.com/photo-1493809842364-78817add7ffb?auto=format&fit=box&w=600&q=80'
        },
        {
          id: 5,
          title: 'Penthouse with Table Mountain Views',
          location: 'City Bowl',
          price: 5500,
          type: 'Penthouse',
          isAvailable: false,
          image: 'https://images.unsplash.com/photo-1600596542815-ffad4c1539a9?auto=format&fit=box&w=600&q=80'
        },
        {
          id: 6,
          title: 'Chic Kalk Bay Loft',
          location: 'Kalk Bay',
          price: 1800,
          type: 'Loft',
          isAvailable: true,
          image: 'https://images.unsplash.com/photo-1560448204-e02f11c3d0e2?auto=format&fit=box&w=600&q=80'
        }
      ],
      // Load bookmarks from LocalStorage if they exist
      bookmarks: JSON.parse(localStorage.getItem('h&b_bookmarks')) || []
    };
  },
  computed: {
    // Dynamic available property counter
    availableCount() {
      return this.properties.filter(p => p.isAvailable).length;
    },
    // Filter and sort logic
    filteredAndSortedProperties() {
      // Filter phase
      let result = this.properties.filter(property => {
        const query = this.searchQuery.toLowerCase();
        return (
          property.title.toLowerCase().includes(query) ||
          property.location.toLowerCase().includes(query)
        );
      });

      // Sort phase
      if (this.sortBy === 'low-high') {
        result.sort((a, b) => a.price - b.price);
      } else if (this.sortBy === 'high-low') {
        result.sort((a, b) => b.price - a.price);
      }

      return result;
    }
  },
  methods: {
    toggleBookmark(id) {
      if (this.bookmarks.includes(id)) {
        this.bookmarks = this.bookmarks.filter(favId => favId !== id);
      } else {
        this.bookmarks.push(id);
      }
      // Save to localStorage
      localStorage.setItem('h&b_bookmarks', JSON.stringify(this.bookmarks));
    },
    
  }
};
</script>

<style scoped>
/* Reset-like scoping and variables */
.listings-container {
  --primary: #2c3e50;
  --accent: #e74c3c;
  --gold: #f1c40f;
  --bg-light: #f8f9fa;
  --text-dark: #333;
  --border-color: #ddd;
  
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  color: var(--text-dark);
}

/* Header*/
.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 2px solid var(--primary);
  padding-bottom: 15px;
  margin-bottom: 30px;
}
.app-header h1 {
  margin: 0;
  color: var(--primary);
  font-size: 2rem;
}
.subtitle {
  margin: 5px 0 0 0;
  color: #7f8c8d;
}
.stats-badge {
  background-color: var(--primary);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 0.9rem;
}

/* Controls (Filters and sorting) */
.controls-section {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
  margin-bottom: 30px;
}
.search-wrapper {
  flex: 1;
  min-width: 280px;
  position: relative;
}
.search-input {
  width: 100%;
  padding: 12px 40px 12px 15px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  box-sizing: border-box;
  font-size: 1rem;
}
.clear-btn {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
  color: #999;
}
.sort-wrapper {
  display: flex;
  align-items: center;
  gap: 10px;
}
.sort-select {
  padding: 11px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  background-color: white;
  font-size: 1rem;
}

/* Grid system */
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 30px;
}
.no-results {
  grid-column: 1 / -1;
  text-align: center;
  padding: 40px;
  color: #7f8c8d;
  font-size: 1.2rem;
}

/* Property Cards */
.property-card {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 15px rgba(0,0,0,0.05);
  border: 1px solid var(--border-color);
  display: flex;
  flex-direction: column;
  position: relative;
  transition: transform 0.2s, box-shadow 0.2s;
}
.property-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0,0,0,0.1);
}

/* Images and UI badges */
.image-wrapper {
  position: relative;
  height: 200px;
  background-color: #eaeded;
}
.property-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.unavailable-ribbon {
  position: absolute;
  top: 15px;
  left: -5px;
  background-color: var(--accent);
  color: white;
  padding: 5px 15px;
  font-weight: bold;
  font-size: 0.8rem;
  text-transform: uppercase;
  box-shadow: 2px 2px 5px rgba(0,0,0,0.2);
  border-radius: 0 4px 4px 0;
}
.bookmark-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: rgba(255, 255, 255, 0.85);
  border: none;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  font-size: 1.3rem;
  color: #ccc;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: color 0.2s, background-color 0.2s;
}
.bookmark-btn:hover {
  background: white;
  color: #999;
}
.bookmark-btn.is-bookmarked {
  color: var(--gold);
  background: white;
  text-shadow: 0 0 2px rgba(0,0,0,0.2);
}

/* Card body content */
.card-content {
  padding: 20px;
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}
.card-meta {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  color: #7f8c8d;
  margin-bottom: 10px;
}
.property-type {
  text-transform: uppercase;
  font-weight: bold;
  letter-spacing: 0.5px;
}
.property-title {
  margin: 0 0 20px 0;
  font-size: 1.25rem;
  color: var(--primary);
  line-height: 1.4;
  flex-grow: 1;
}
.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
}
.property-price {
  font-size: 1.3rem;
  font-weight: bold;
  color: var(--primary);
}
.property-price small {
  font-size: 0.8rem;
  font-weight: normal;
  color: #7f8c8d;
}
.contact-btn {
  background-color: var(--primary);
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.2s;
}
.contact-btn:hover:not(:disabled) {
  background-color: #34495e;
}

/* Disabled/Unavailable property cards */
.is-unavailable {
  opacity: 0.75;
}
.contact-btn:disabled {
  background-color: #bdc3c7;
  cursor: not-allowed;
}

/* Responsive breakpoint */
@media (max-width: 600px) {
  .app-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 15px;
  }
  .controls-section {
    flex-direction: column;
    gap: 15px;
  }
  .sort-wrapper {
    width: 100%;
    justify-content: space-between;
  }
  .sort-select {
    flex-grow: 1;
  }
}
</style>