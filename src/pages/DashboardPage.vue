<style scoped>
.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 1rem;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f4f4f4;
  color: #333;
  font-weight: bold;
}

tr:hover {
  background-color: #f9f9f9;
}

.status-shipped {
  background-color: #d1fae5;
  color: #065f46;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.status-in-production {
  background-color: #fef08a;
  color: #854d0e;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.status-canceled {
  background-color: #fecaca;
  color: #991b1b;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.popup-content {
  background: white;
  padding: 2rem;
  border-radius: 10px;
  max-width: 500px;
  width: 100%;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.popup-content h2 {
  font-size: 1.5rem;
  font-weight: bold;
}

.popup-content p {
  margin-bottom: 0.5rem;
}

.popup-content select {
  margin-top: 0.5rem;
  width: 100%;
  padding: 0.5rem;
}

.popup-content button {
  margin-top: 1rem;
}

.status-shipped {
  background-color: #d1fae5;
  color: #065f46;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.status-in-production {
  background-color: #fef08a;
  color: #854d0e;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.status-canceled {
  background-color: #fecaca;
  color: #991b1b;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-weight: bold;
}

.view-details {
  color: #fff;
  background-color: #000;
  border: 1px solid transparent;
  padding: 0.5rem 1rem;
  text-decoration: none;
  text-align: center;
  display: inline-block;
  transition: all 0.1s;
}

.view-details:hover {
  background-color: white;
  color: #000;
  border: 1px solid #000;
}

.actions {
  display: flex;
  gap: 0.5rem;
}

.delete-btn {
  color: #fff;
  background-color: #e3342f;
  border: none;
  padding: 0.5rem 1rem;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s;
}

.delete-btn:hover {
  background-color: #cc1f1a;
}
</style>

<template>
  <div class="min-h-screen flex bg-gray-100">
    <Sidebar />

    <!-- Main Content -->
    <main class="flex-1 p-8 bg-white">
      <h1 class="text-2xl font-bold text-gray-800 mb-4">Orders Overview</h1>

      <div class="mb-6">
        <p class="text-lg text-gray-600">
          Total Orders: <span class="font-bold text-black">{{ totalOrders }}</span>
        </p>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>Customer</th>
              <th>Order Date</th>
              <th>Status</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="order in orders" :key="order.id">
              <td>{{ order.customer }}</td>
              <td>{{ order.date }}</td>
              <td>
                <span
                  :class="{
                    'status-in-production': order.status === 'in-production',
                    'status-shipped': order.status === 'shipped',
                    'status-canceled': order.status === 'canceled',
                  }"
                >
                  {{ order.status }}
                </span>
              </td>
              <td>
                <div class="actions">
                  <button @click="viewOrderDetails(order)" class="view-details">
                    View Details
                  </button>
                  <button @click="deleteOrder(order.id)" class="delete-btn">Delete</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Popup voor Order Details -->
      <div v-if="selectedOrder" class="popup-overlay">
        <div class="popup-content">
          <h2>Order Details</h2>
          <p><strong>Customer:</strong> {{ selectedOrder.customer }}</p>
          <p><strong>Order Date:</strong> {{ selectedOrder.date }}</p>
          <p><strong>Status:</strong> {{ selectedOrder.status }}</p>
          <select
            v-model="selectedOrder.status"
            @change="updateOrderStatus(selectedOrder.id, selectedOrder.status)"
          >
            <option value="in-production">In Production</option>
            <option value="shipped">Shipped</option>
            <option value="canceled">Canceled</option>
          </select>
          <button @click="closePopup" class="mt-4 bg-black text-white py-2 px-4 rounded">
            Close
          </button>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import Sidebar from '../components/Sidebar.vue';

export default {
  components: { Sidebar },
  setup() {
    const totalOrders = ref(0)
    const orders = ref([])
    const selectedOrder = ref(null)

    // Fetch Orders
    const fetchOrders = async () => {
      try {
        const response = await fetch(
          'https://sneaker-configurator-backend.onrender.com/api/v1/orders',
          {
            headers: {
              Authorization: `Bearer ${localStorage.getItem('token')}`,
            },
          },
        )
        const data = await response.json()
        if (data.status === 'success') {
          orders.value = data.data.orders.map((order) => ({
            id: order._id,
            customer: order.customerName || order.customerEmail,
            date: new Date(order.createdAt).toLocaleDateString(),
            status: order.status,
          }))
          totalOrders.value = orders.value.length
        } else {
          console.error('Failed to fetch orders:', data.message)
        }
      } catch (error) {
        console.error('Error fetching orders:', error)
      }
    }

    // View Order Details
    const viewOrderDetails = (order) => {
      selectedOrder.value = { ...order }
    }

    // Close Popup
    const closePopup = () => {
      selectedOrder.value = null
    }

    // Delete Order
    const deleteOrder = async (id) => {
      try {
        const response = await fetch(
          `https://sneaker-configurator-backend.onrender.com/api/v1/orders/${id}`,
          {
            method: 'DELETE',
            headers: {
              Authorization: `Bearer ${localStorage.getItem('token')}`,
            },
          },
        )
        if (response.ok) {
          orders.value = orders.value.filter((order) => order.id !== id)
          totalOrders.value = orders.value.length
        } else {
          console.error('Failed to delete order')
        }
      } catch (error) {
        console.error('Error deleting order:', error)
      }
    }

    // Update Order Status
    const updateOrderStatus = async (id, status) => {
      try {
        const response = await fetch(
          `https://sneaker-configurator-backend.onrender.com/api/v1/orders/${id}`,
          {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json',
              Authorization: `Bearer ${localStorage.getItem('token')}`,
            },
            body: JSON.stringify({ status }),
          },
        )
        if (response.ok) {
          const updatedOrder = orders.value.find((order) => order.id === id)
          if (updatedOrder) {
            updatedOrder.status = status // Update status live
          }
          console.log('Order status updated successfully')
        } else {
          console.error('Failed to update order status')
        }
      } catch (error) {
        console.error('Error updating order status:', error)
      }
    }

    onMounted(() => {
      fetchOrders()
    })

    return {
      totalOrders,
      orders,
      selectedOrder,
      viewOrderDetails,
      closePopup,
      deleteOrder,
      updateOrderStatus,
    }
  },
}
</script>
