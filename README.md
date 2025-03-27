<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Estoque CIMCOP</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --accent-color: #1abc9c;
            --danger-color: #e74c3c;
            --warning-color: #f39c12;
            --success-color: #2ecc71;
            --text-color: #333;
            --light-text: #ecf0f1;
            --dark-bg: #1a1a2e;
            --card-bg: #ffffff;
            --hover-color: #16a085;
            --border-radius: 12px;
            --box-shadow: 0 10px 20px rgba(0,0,0,0.1);
            --transition: all 0.3s ease;
            --gradient-bg: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            --glass-bg: rgba(255, 255, 255, 0.1);
            --glass-border: 1px solid rgba(255, 255, 255, 0.2);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            text-transform: uppercase;
        }

        body {
            background: var(--gradient-bg);
            color: var(--light-text);
            min-height: 100vh;
            line-height: 1.6;
            overflow-x: hidden;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 0 20px;
        }

        /* Modern Header */
        header {
            background: rgba(26, 26, 46, 0.8);
            padding: 15px 0;
            border-bottom: var(--glass-border);
            position: sticky;
            top: 0;
            z-index: 80;
        }

        .header-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .logo {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .logo img {
            height: 30px;
        }

        .logo h1 {
            font-size: 1.2rem;
            font-weight: 600;
            color: var(--light-text);
            margin: 0;
        }

        .header-actions {
            display: flex;
            gap: 15px;
        }

        /* Modern Button Styles */
        .btn {
            background: var(--glass-bg);
            color: var(--light-text);
            border: var(--glass-border);
            padding: 10px 20px;
            border-radius: var(--border-radius);
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: var(--transition);
            font-weight: 500;
            backdrop-filter: blur(5px);
        }

        .btn:hover {
            background: var(--secondary-color);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(52, 152, 219, 0.3);
        }

        .btn-primary {
            background: var(--secondary-color);
        }

        .btn-danger {
            background: var(--danger-color);
        }

        .btn-success {
            background: var(--success-color);
        }

        .btn-warning {
            background: var(--warning-color);
        }

        /* Main Content */
        .main-content {
            display: grid;
            grid-template-columns: 250px 1fr;
            gap: 20px;
            margin-top: 20px;
        }

        /* Sidebar */
        .sidebar {
            background: rgba(26, 26, 46, 0.5);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 20px;
            height: calc(100vh - 100px);
            position: sticky;
            top: 20px;
            border: var(--glass-border);
            overflow-y: auto;
        }

        .sidebar-section {
            margin-bottom: 25px;
        }

        .sidebar-section h3 {
            font-size: 1.1rem;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 8px;
            color: var(--light-text);
        }

        .sidebar-section .stats {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        .stat-card {
            background: var(--glass-bg);
            border: var(--glass-border);
            border-radius: var(--border-radius);
            padding: 15px;
            text-align: center;
            transition: var(--transition);
        }

        .stat-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0,0,0,0.2);
        }

        .stat-card .number {
            font-size: 1.8rem;
            font-weight: bold;
            color: var(--secondary-color);
            margin-bottom: 5px;
        }

        .stat-card .label {
            font-size: 0.8rem;
            color: var(--light-text);
        }

        /* Letter Navigation */
        .letter-nav {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 15px;
            justify-content: center;
        }

        .letter-item {
            width: 35px;
            height: 35px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: var(--glass-bg);
            border: var(--glass-border);
            border-radius: 50%;
            cursor: pointer;
            transition: var(--transition);
            font-weight: 500;
            position: relative;
        }

        .letter-item:hover, .letter-item.active {
            background: var(--secondary-color);
            transform: scale(1.1);
        }

        .letter-indicator {
            width: 6px;
            height: 6px;
            background-color: #e74c3c;
            border-radius: 50%;
            position: absolute;
            top: 5px;
            right: 5px;
        }

        .letter-item.disabled {
            opacity: 0.5;
            cursor: default;
        }

        /* Search Section */
        .search-section {
            background: rgba(26, 26, 46, 0.5);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 20px;
            margin-bottom: 20px;
            border: var(--glass-border);
        }

        .search-container {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .search-input {
            flex: 1;
            padding: 12px 15px;
            border-radius: var(--border-radius);
            border: var(--glass-border);
            background: rgba(255, 255, 255, 0.05);
            color: var(--light-text);
            font-size: 1rem;
        }

        .search-input::placeholder {
            color: rgba(255, 255, 255, 0.5);
        }

        .filters {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
        }

        .filter-select {
            padding: 10px 15px;
            border-radius: var(--border-radius);
            border: var(--glass-border);
            background: rgba(0, 0, 0, 0.3);
            color: var(--light-text);
            min-width: 150px;
        }

        /* Inventory Cards */
        .inventory-container {
            display: grid;
            gap: 20px;
        }

        .division-card {
            background: rgba(26, 26, 46, 0.5);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            overflow: hidden;
            border: var(--glass-border);
            transition: var(--transition);
        }

        .division-card:hover {
            transform: translateY(-5px);
            box-shadow: var(--box-shadow);
        }

        .division-header {
            padding: 15px 20px;
            background: rgba(52, 152, 219, 0.2);
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
        }

        .division-header h3 {
            font-size: 1.1rem;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .division-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease;
        }

        .division-card.expanded .division-content {
            max-height: 2000px;
        }

        .division-items {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 15px;
            padding: 20px;
        }

        .division-item {
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 15px;
            border: var(--glass-border);
            transition: var(--transition);
            position: relative;
            overflow: hidden;
        }

        .division-item:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            background: rgba(255, 255, 255, 0.1);
        }

        .division-item[data-location="A1"] { 
            border-left: 4px solid #3498db; 
            background-color: rgba(52, 152, 219, 0.1); 
        }

        .division-item[data-location="B1"] { 
            border-left: 4px solid #2ecc71; 
            background-color: rgba(46, 204, 113, 0.1); 
        }

        .division-item[data-location="C1"] { 
            border-left: 4px solid #f39c12; 
            background-color: rgba(243, 156, 18, 0.1); 
        }

        .division-item[data-location="D1"] { 
            border-left: 4px solid #e74c3c; 
            background-color: rgba(231, 76, 60, 0.1); 
        }

        .item-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }

        .item-code {
            font-weight: bold;
            color: var(--secondary-color);
        }

        .item-location {
            background: var(--secondary-color);
            color: white;
            padding: 3px 8px;
            border-radius: 20px;
            font-size: 0.8rem;
        }

        .item-quantity {
            background: var(--success-color);
            color: white;
            padding: 3px 8px;
            border-radius: 20px;
            font-size: 0.8rem;
        }

        .item-category {
            font-size: 0.8rem;
            color: var(--warning-color);
            margin-bottom: 5px;
        }

        .item-name {
            font-weight: 600;
            margin-bottom: 10px;
            color: var(--light-text);
        }

        .item-details {
            font-size: 0.9rem;
            color: rgba(255, 255, 255, 0.7);
            margin-bottom: 15px;
        }

        .details-button {
            background: var(--glass-bg);
            color: var(--light-text);
            border: var(--glass-border);
            padding: 8px 15px;
            border-radius: var(--border-radius);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 5px;
            width: 100%;
            transition: var(--transition);
        }

        .details-button:hover {
            background: var(--secondary-color);
        }

        /* Modal Styles */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background: var(--dark-bg);
            border-radius: var(--border-radius);
            max-width: 800px;
            width: 90%;
            margin: 50px auto;
            padding: 25px;
            position: relative;
            border: var(--glass-border);
            max-height: 80vh;
            overflow-y: auto;
        }

        .close-modal {
            position: absolute;
            top: 15px;
            right: 15px;
            font-size: 1.5rem;
            cursor: pointer;
            color: var(--light-text);
            transition: var(--transition);
        }

        .close-modal:hover {
            color: var(--danger-color);
            transform: rotate(90deg);
        }

        .modal-header {
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .modal-header h2 {
            font-size: 1.5rem;
            color: var(--light-text);
        }

        .form-input {
            width: 100%;
            padding: 12px 15px;
            border-radius: var(--border-radius);
            border: var(--glass-border);
            background: rgba(0, 0, 0, 0.3);
            color: var(--light-text);
            margin-top: 5px;
        }

        /* Admin Panel */
        .admin-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            overflow-x: auto;
            padding-bottom: 5px;
        }

        .tab-button {
            padding: 10px 20px;
            background: var(--glass-bg);
            border: var(--glass-border);
            border-radius: var(--border-radius);
            cursor: pointer;
            white-space: nowrap;
            transition: var(--transition);
        }

        .tab-button.active, .tab-button:hover {
            background: var(--secondary-color);
        }

        .admin-section {
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 20px;
            margin-bottom: 20px;
            border: var(--glass-border);
        }

        .admin-section h4 {
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .action-button {
            background: var(--glass-bg);
            color: var(--light-text);
            border: var(--glass-border);
            padding: 10px 20px;
            border-radius: var(--border-radius);
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: var(--transition);
        }

        .action-button:hover {
            background: var(--secondary-color);
            transform: translateY(-2px);
        }

        .action-button.danger {
            background: rgba(231, 76, 60, 0.2);
        }

        .action-button.danger:hover {
            background: var(--danger-color);
        }

        .danger-zone {
            border: 1px solid var(--danger-color);
            background: rgba(231, 76, 60, 0.1);
        }

        /* Mobile Navigation */
        .mobile-nav {
            display: none;
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background: rgba(26, 26, 46, 0.9);
            backdrop-filter: blur(10px);
            padding: 15px;
            z-index: 90;
            border-top: var(--glass-border);
        }

        #mobile-menu-btn {
            background: var(--secondary-color);
            color: white;
            border: none;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(52, 152, 219, 0.3);
            position: absolute;
            bottom: 20px;
            right: 20px;
            z-index: 100;
        }

        .mobile-menu {
            display: none;
            flex-direction: column;
            gap: 10px;
            padding: 10px 0;
        }

        .mobile-menu.active {
            display: flex;
        }

        .mobile-menu button {
            background: var(--glass-bg);
            color: var(--light-text);
            border: var(--glass-border);
            padding: 12px;
            border-radius: var(--border-radius);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            transition: var(--transition);
            font-weight: 500;
        }

        .mobile-menu button:hover {
            background: var(--secondary-color);
        }

        /* Footer */
        footer {
            text-align: center;
            padding: 20px;
            margin-top: 40px;
            color: rgba(255, 255, 255, 0.5);
            font-size: 0.9rem;
            border-top: var(--glass-border);
        }

        /* Responsive Design */
        @media (max-width: 1024px) {
            .main-content {
                grid-template-columns: 1fr;
            }

            .sidebar {
                height: auto;
                position: static;
            }
        }

        @media (max-width: 768px) {
            .header-actions {
                display: none;
            }

            .division-items {
                grid-template-columns: 1fr;
            }

            .mobile-nav {
                display: block;
            }

            .filters {
                flex-direction: column;
            }

            .search-container {
                flex-direction: column;
            }

            .modal-content {
                width: 95%;
                margin: 20px auto;
            }
        }

        /* Animation Effects */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .division-card {
            animation: fadeIn 0.5s ease forwards;
        }

        /* Card Filters */
        .card-filters {
            padding: 10px 20px;
            background: rgba(0, 0, 0, 0.1);
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .filter-group {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .filter-group label {
            font-size: 0.9rem;
            color: var(--light-text);
        }

        .filter-group select {
            padding: 5px 10px;
            border-radius: var(--border-radius);
            border: var(--glass-border);
            background: rgba(0, 0, 0, 0.3);
            color: var(--light-text);
        }

        /* Admin Login */
        .admin-login-container {
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .login-form {
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 30px;
            width: 100%;
            max-width: 400px;
            text-align: center;
            border: var(--glass-border);
        }

        .login-icon {
            font-size: 3rem;
            color: var(--secondary-color);
            margin-bottom: 20px;
        }

        /* OT History */
        .ot-record {
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 15px;
            margin-bottom: 15px;
            border: var(--glass-border);
        }

        .ot-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .ot-id {
            font-weight: bold;
            color: var(--secondary-color);
        }

        .ot-date {
            color: var(--light-text);
        }

        .ot-details {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-bottom: 15px;
        }

        .ot-actions {
            display: flex;
            justify-content: flex-end;
        }

        /* Print Styles */
        @media print {
            body * {
                visibility: hidden;
            }
            .print-container, .print-container * {
                visibility: visible;
            }
            .print-container {
                position: absolute;
                left: 0;
                top: 0;
                width: 100%;
                background: white;
                color: black;
            }
            .print-table {
                width: 100%;
                border-collapse: collapse;
            }
            .print-table th, .print-table td {
                border: 1px solid #ddd;
                padding: 8px;
                text-align: left;
            }
            .print-table th {
                background-color: #f2f2f2;
            }
            .btn, .card-filters, .mobile-nav {
                display: none !important;
            }
        }

        /* List View Modal */
        .list-view-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .list-view-content {
            background: var(--dark-bg);
            border-radius: var(--border-radius);
            max-width: 90%;
            width: 800px;
            margin: 50px auto;
            padding: 25px;
            position: relative;
            border: var(--glass-border);
            max-height: 80vh;
            overflow-y: auto;
        }

        .list-view-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        .list-view-table th, .list-view-table td {
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 8px;
            text-align: left;
        }

        .list-view-table th {
            background: rgba(52, 152, 219, 0.2);
        }

        .list-view-table tr:nth-child(even) {
            background: rgba(255, 255, 255, 0.05);
        }

        /* Dashboard Modal */
        .dashboard-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .dashboard-content {
            background: var(--dark-bg);
            border-radius: var(--border-radius);
            max-width: 90%;
            width: 1000px;
            margin: 50px auto;
            padding: 25px;
            position: relative;
            border: var(--glass-border);
            max-height: 80vh;
            overflow-y: auto;
        }

        .dashboard-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 20px;
            margin-top: 20px;
        }

        .dashboard-card {
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 20px;
            border: var(--glass-border);
        }

        .dashboard-card h4 {
            margin-bottom: 15px;
            color: var(--secondary-color);
        }

        .dashboard-stats {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 15px;
        }

        .dashboard-stat {
            background: rgba(255, 255, 255, 0.1);
            border-radius: var(--border-radius);
            padding: 15px;
            text-align: center;
        }

        .dashboard-stat-value {
            font-size: 1.8rem;
            font-weight: bold;
            color: var(--accent-color);
        }

        .dashboard-stat-label {
            font-size: 0.9rem;
            color: var(--light-text);
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }

        .dashboard-chart {
            height: 300px;
            margin-top: 20px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: var(--border-radius);
            padding: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--light-text);
            position: relative;
        }

        .chart-label {
            position: absolute;
            top: 10px;
            left: 10px;
            font-weight: bold;
        }

        .chart-total {
            position: absolute;
            font-size: 2rem;
            font-weight: bold;
            color: var(--accent-color);
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="header-content">
                <div class="logo">
                    <img src="https://i.ibb.co/0VyYKJnK/cimcop-logo.png" alt="CIMCOP Logo">
                    <h1>ESTOQUE CENTRAL</h1>
                </div>
                <div class="header-actions">
                    <button id="print-button" class="btn">
                        <i class="fas fa-print"></i> IMPRIMIR
                    </button>
                    <button id="export-excel-button" class="btn">
                        <i class="fas fa-file-excel"></i> EXPORTAR
                    </button>
                    <button id="admin-button" class="btn btn-primary">
                        <i class="fas fa-cog"></i> ADMIN
                    </button>
                </div>
            </div>
        </header>
        
        <div class="main-content">
            <div class="sidebar">
                <div class="sidebar-section">
                    <h3><i class="fas fa-chart-pie"></i> ESTATÍSTICAS</h3>
                    <div class="stats">
                        <div class="stat-card">
                            <div class="number" id="total-items">0</div>
                            <div class="label">ITENS</div>
                        </div>
                        <div class="stat-card">
                            <div class="number" id="total-locations">0</div>
                            <div class="label">LOCALIZAÇÕES</div>
                        </div>
                    </div>
                </div>
                
                <div class="sidebar-section">
                    <h3><i class="fas fa-map-marker-alt"></i> LOCALIZAÇÕES</h3>
                    <div id="letter-list" class="letter-nav">
                        <!-- Letter list will be generated dynamically -->
                    </div>
                    <button id="list-view-all-btn" class="btn" style="width: 100%; margin-top: 10px;">
                        <i class="fas fa-list"></i> VISUALIZAR EM LISTA
                    </button>
                </div>
            </div>
            
            <div class="inventory-container">
                <div class="search-section">
                    <div class="search-container">
                        <input type="text" id="search-input" class="search-input" placeholder="BUSCAR POR CÓDIGO, NOME OU DESCRIÇÃO...">
                        <button id="search-button" class="btn btn-primary">
                            <i class="fas fa-search"></i> BUSCAR
                        </button>
                    </div>
                    <div class="filters">
                        <select id="category-filter" class="filter-select">
                            <option value="all">TODAS AS CATEGORIAS</option>
                            <!-- Categories will be loaded dynamically -->
                        </select>
                        <select id="location-filter" class="filter-select">
                            <option value="all">TODAS AS LOCALIZAÇÕES</option>
                            <!-- Locations will be loaded dynamically -->
                        </select>
                        <button id="analytics-button" class="btn">
                            <i class="fas fa-chart-bar"></i> ANÁLISE DE DADOS
                        </button>
                    </div>
                </div>
                
                <div id="division-container">
                    <!-- Division cards will be loaded dynamically -->
                </div>
            </div>
        </div>
        
        <!-- Item Details Modal -->
        <div id="item-modal" class="modal">
            <div class="modal-content">
                <span class="close-modal">&times;</span>
                <div class="modal-header">
                    <h2 id="modal-item-name">DETALHES DO ITEM</h2>
                </div>
                <div id="modal-details" class="modal-details">
                    <!-- Item details will be loaded dynamically -->
                </div>
            </div>
        </div>
        
        <!-- Admin Modal -->
        <div id="admin-modal" class="modal">
            <div class="modal-content admin-modal-content">
                <span class="close-modal">&times;</span>
                <div class="admin-header">
                    <h3><i class="fas fa-cog"></i> PAINEL DE ADMINISTRAÇÃO</h3>
                </div>
                
                <div id="admin-login" class="admin-login-container">
                    <div class="login-form">
                        <div class="login-icon">
                            <i class="fas fa-user-lock"></i>
                        </div>
                        <h4>ACESSO RESTRITO</h4>
                        <input type="password" id="admin-password" placeholder="SENHA" class="form-input">
                        <div id="login-error" style="color: var(--danger-color); margin: 10px 0; display: none;">
                            SENHA INCORRETA. TENTE NOVAMENTE.
                        </div>
                        <button id="login-button" class="action-button">ENTRAR</button>
                    </div>
                </div>
                
                <div id="admin-panel" style="display:none;">
                    <div class="admin-tabs">
                        <button class="tab-button active" data-tab="inventory-management">
                            <i class="fas fa-boxes"></i> GERENCIAR ESTOQUE
                        </button>
                        <button class="tab-button" data-tab="transfer-management">
                            <i class="fas fa-exchange-alt"></i> TRANSFERÊNCIAS
                        </button>
                        <button class="tab-button" data-tab="receipt-management">
                            <i class="fas fa-truck-loading"></i> RECEBIMENTOS
                        </button>
                        <button class="tab-button" data-tab="settings-management">
                            <i class="fas fa-tools"></i> CONFIGURAÇÕES
                        </button>
                        <button class="tab-button" data-tab="activity-log">
                            <i class="fas fa-clipboard-list"></i> HISTÓRICO
                        </button>
                        <button class="tab-button" data-tab="ot-history">
                            <i class="fas fa-history"></i> OTS
                        </button>
                    </div>
                    
                    <div id="inventory-management" class="admin-panel-content">
                        <div class="admin-section">
                            <h4><i class="fas fa-plus-circle"></i> ADICIONAR NOVO ITEM</h4>
                            <form id="add-item-form">
                                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                                    <div>
                                        <label for="item-code">CÓDIGO:</label>
                                        <input type="text" id="item-code" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="item-name">NOME:</label>
                                        <input type="text" id="item-name" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="item-quantity">QUANTIDADE:</label>
                                        <input type="number" id="item-quantity" class="form-input" required min="0">
                                    </div>
                                    <div>
                                        <label for="item-location">LOCALIZAÇÃO:</label>
                                        <input type="text" id="item-location" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="item-category">CATEGORIA:</label>
                                        <select id="item-category" class="form-input">
                                            <option value="110">110 - COMBUSTIVEIS</option>
                                            <option value="120">120 - CORREIAS, POLIAS E TENSORES</option>
                                            <option value="140">140 - FERRAMENTAS</option>
                                            <option value="150">150 - FILTROS</option>
                                            <option value="160">160 - LUBRIFICANTES</option>
                                            <option value="170">170 - PECAS P/ SISTEMA HID. E PNEUM.</option>
                                            <option value="180">180 - MATERIAL DE DESGASTE RAPIDO</option>
                                            <option value="190">190 - MATERIAIS ELETRICOS</option>
                                            <option value="200">200 - MATERIAIS DE CONSUMO/OFICINA</option>
                                            <option value="220">220 - MATERIAL RODANTE</option>
                                            <option value="230">230 - PNEUS E ASSOCIADOS</option>
                                            <option value="240">240 - PORCAS, PARAFUSOS E ARRUELAS</option>
                                            <option value="250">250 - PECAS P/ MANCAIS E ARTICULACOES</option>
                                            <option value="270">270 - SERVICOS DE TERCEIROS</option>
                                            <option value="280">280 - PECAS P/ EQUIPAMENTOS</option>
                                            <option value="400">400 - MATERIAL DE EXPEDIENTE/ESCRIT.</option>
                                            <option value="420">420 - EPI MATERIAIS DE SEGURANCA (INFORMAR CA)</option>
                                            <option value="440">440 - MATERIAL DE CONSTRUCAO</option>
                                            <option value="450">450 - BENS DE NATUREZA PERMANENTE</option>
                                        </select>
                                    </div>
                                    <div style="grid-column: span 2;">
                                        <label for="item-description">DESCRIÇÃO:</label>
                                        <textarea id="item-description" class="form-input" rows="3"></textarea>
                                    </div>
                                    <div>
                                        <label for="item-equivalencia">EQUIVALÊNCIA:</label>
                                        <input type="text" id="item-equivalencia" class="form-input">
                                    </div>
                                    <div>
                                        <label for="item-aplicacao">APLICAÇÃO:</label>
                                        <input type="text" id="item-aplicacao" class="form-input">
                                    </div>
                                    <div>
                                        <label for="item-ca">CA EPI:</label>
                                        <select id="item-ca" class="form-input">
                                            <option value="Sim">SIM</option>
                                            <option value="Não">NÃO</option>
                                            <option value="N/A">N/A</option>
                                        </select>
                                    </div>
                                </div>
                                <button type="submit" class="btn btn-success" style="margin-top: 20px; width: 100%;">
                                    <i class="fas fa-save"></i> SALVAR ITEM
                                </button>
                            </form>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-file-import"></i> IMPORTAR DADOS</h4>
                            <p>IMPORTE DADOS DE ESTOQUE A PARTIR DE UMA PLANILHA EXCEL.</p>
                            <button id="import-excel-button" class="btn" style="width: 100%; margin-top: 10px;">
                                <i class="fas fa-file-excel"></i> SELECIONAR ARQUIVO EXCEL
                            </button>
                            <button id="download-template-button" class="btn" style="width: 100%; margin-top: 10px;">
                                <i class="fas fa-download"></i> BAIXAR MODELO
                            </button>
                        </div>
                        
                        <div class="admin-section danger-zone">
                            <h4><i class="fas fa-exclamation-triangle"></i> ZONA DE PERIGO</h4>
                            <p>ESTAS AÇÕES SÃO IRREVERSÍVEIS. TENHA CUIDADO.</p>
                            <div style="display: flex; gap: 10px; margin-top: 15px;">
                                <button id="clear-data-button" class="action-button danger" style="flex: 1;">
                                    <i class="fas fa-trash-alt"></i> LIMPAR TODOS OS DADOS
                                </button>
                                <button id="reset-settings-button" class="action-button danger" style="flex: 1;">
                                    <i class="fas fa-undo"></i> REDEFINIR CONFIGURAÇÕES
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div id="transfer-management" class="admin-panel-content" style="display: none;">
                        <div class="admin-section">
                            <h4><i class="fas fa-exchange-alt"></i> NOVA TRANSFERÊNCIA</h4>
                            <form id="transfer-form">
                                <div style="margin-bottom: 20px;">
                                    <label for="transfer-item">ITEM:</label>
                                    <select id="transfer-item" class="form-input" required>
                                        <option value="">SELECIONE UM ITEM</option>
                                        <!-- Items will be loaded dynamically -->
                                    </select>
                                </div>
                                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                                    <div>
                                        <label for="transfer-from">LOCALIZAÇÃO ATUAL:</label>
                                        <input type="text" id="transfer-from" class="form-input" readonly>
                                    </div>
                                    <div>
                                        <label for="transfer-to">NOVA LOCALIZAÇÃO:</label>
                                        <input type="text" id="transfer-to" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="transfer-quantity">QUANTIDADE:</label>
                                        <input type="number" id="transfer-quantity" class="form-input" required min="1">
                                    </div>
                                    <div>
                                        <label for="transfer-date">DATA:</label>
                                        <input type="date" id="transfer-date" class="form-input" required>
                                    </div>
                                </div>
                                <div style="margin-top: 15px;">
                                    <label for="transfer-reason">MOTIVO:</label>
                                    <textarea id="transfer-reason" class="form-input" rows="3"></textarea>
                                </div>
                                <button type="submit" class="btn btn-success" style="margin-top: 20px; width: 100%;">
                                    <i class="fas fa-save"></i> REGISTRAR TRANSFERÊNCIA
                                </button>
                            </form>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-history"></i> HISTÓRICO DE TRANSFERÊNCIAS</h4>
                            <div id="transfer-history">
                                <!-- Transfer history will be loaded dynamically -->
                                <p>NENHUMA TRANSFERÊNCIA REGISTRADA.</p>
                            </div>
                        </div>
                    </div>
                    
                    <div id="receipt-management" class="admin-panel-content" style="display: none;">
                        <div class="admin-section">
                            <h4><i class="fas fa-truck-loading"></i> NOVO RECEBIMENTO</h4>
                            <form id="receipt-form">
                                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                                    <div>
                                        <label for="receipt-item">ITEM:</label>
                                        <select id="receipt-item" class="form-input">
                                            <option value="new">NOVO ITEM</option>
                                            <!-- Existing items will be loaded dynamically -->
                                        </select>
                                    </div>
                                    <div>
                                        <label for="receipt-quantity">QUANTIDADE:</label>
                                        <input type="number" id="receipt-quantity" class="form-input" required min="1">
                                    </div>
                                    <div>
                                        <label for="receipt-date">DATA:</label>
                                        <input type="date" id="receipt-date" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="receipt-supplier">FORNECEDOR:</label>
                                        <input type="text" id="receipt-supplier" class="form-input">
                                    </div>
                                    <div>
                                        <label for="receipt-invoice">NOTA FISCAL:</label>
                                        <input type="text" id="receipt-invoice" class="form-input">
                                    </div>
                                    <div>
                                        <label for="receipt-location">LOCALIZAÇÃO:</label>
                                        <input type="text" id="receipt-location" class="form-input" required>
                                    </div>
                                </div>
                                <div style="margin-top: 15px;">
                                    <label for="receipt-notes">OBSERVAÇÕES:</label>
                                    <textarea id="receipt-notes" class="form-input" rows="3"></textarea>
                                </div>
                                <button type="submit" class="btn btn-success" style="margin-top: 20px; width: 100%;">
                                    <i class="fas fa-save"></i> REGISTRAR RECEBIMENTO
                                </button>
                            </form>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-history"></i> HISTÓRICO DE RECEBIMENTOS</h4>
                            <div id="receipt-history">
                                <!-- Receipt history will be loaded dynamically -->
                                <p>NENHUM RECEBIMENTO REGISTRADO.</p>
                            </div>
                        </div>
                    </div>
                    
                    <div id="settings-management" class="admin-panel-content" style="display: none;">
                        <div class="admin-section">
                            <h4><i class="fas fa-palette"></i> APARÊNCIA</h4>
                            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 15px;">
                                <div>
                                    <label for="theme-select">TEMA:</label>
                                    <select id="theme-select" class="form-input">
                                        <option value="dark">ESCURO (PADRÃO)</option>
                                        <option value="light">CLARO</option>
                                        <option value="blue">AZUL</option>
                                        <option value="green">VERDE</option>
                                    </select>
                                </div>
                                <div>
                                    <label for="font-size">TAMANHO DA FONTE:</label>
                                    <select id="font-size" class="form-input">
                                        <option value="small">PEQUENO</option>
                                        <option value="medium" selected>MÉDIO</option>
                                        <option value="large">GRANDE</option>
                                    </select>
                                </div>
                            </div>
                            <button id="apply-theme" class="btn" style="margin-top: 15px; width: 100%;">
                                <i class="fas fa-check"></i> APLICAR ALTERAÇÕES
                            </button>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-database"></i> DADOS</h4>
                            <div style="margin-top: 15px;">
                                <button id="backup-data" class="btn" style="width: 100%; margin-bottom: 10px;">
                                    <i class="fas fa-download"></i> FAZER BACKUP DOS DADOS
                                </button>
                                <button id="restore-data" class="btn" style="width: 100%;">
                                    <i class="fas fa-upload"></i> RESTAURAR DADOS
                                </button>
                            </div>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-user-shield"></i> SEGURANÇA</h4>
                            <div style="margin-top: 15px;">
                                <label for="new-password">NOVA SENHA:</label>
                                <input type="password" id="new-password" class="form-input">
                            </div>
                            <div style="margin-top: 15px;">
                                <label for="confirm-password">CONFIRMAR SENHA:</label>
                                <input type="password" id="confirm-password" class="form-input">
                            </div>
                            <button id="change-password" class="btn" style="margin-top: 15px; width: 100%;">
                                <i class="fas fa-key"></i> ALTERAR SENHA
                            </button>
                        </div>
                    </div>
                    
                    <div id="activity-log" class="admin-panel-content" style="display: none;">
                        <div class="admin-section">
                            <h4><i class="fas fa-clipboard-list"></i> REGISTRO DE ATIVIDADES</h4>
                            <div style="margin: 15px 0;">
                                <label for="log-filter">FILTRAR POR:</label>
                                <select id="log-filter" class="form-input">
                                    <option value="all">TODAS AS ATIVIDADES</option>
                                    <option value="add">ADIÇÕES</option>
                                    <option value="edit">EDIÇÕES</option>
                                    <option value="delete">EXCLUSÕES</option>
                                    <option value="transfer">TRANSFERÊNCIAS</option>
                                    <option value="receipt">RECEBIMENTOS</option>
                                </select>
                            </div>
                            <div id="activity-log-container" style="max-height: 400px; overflow-y: auto;">
                                <!-- Activity log will be loaded dynamically -->
                                <p>NENHUMA ATIVIDADE REGISTRADA.</p>
                            </div>
                        </div>
                    </div>
                    
                    <div id="ot-history" class="admin-panel-content" style="display: none;">
                        <div class="admin-section">
                            <h4><i class="fas fa-file-alt"></i> NOVA OT</h4>
                            <form id="ot-form">
                                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                                    <div>
                                        <label for="ot-number">NÚMERO DA OT:</label>
                                        <input type="text" id="ot-number" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="ot-date">DATA:</label>
                                        <input type="date" id="ot-date" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="ot-requester">SOLICITANTE:</label>
                                        <input type="text" id="ot-requester" class="form-input" required>
                                    </div>
                                    <div>
                                        <label for="ot-department">DEPARTAMENTO:</label>
                                        <input type="text" id="ot-department" class="form-input" required>
                                    </div>
                                </div>
                                <div style="margin-top: 15px;">
                                    <label for="ot-description">DESCRIÇÃO:</label>
                                    <textarea id="ot-description" class="form-input" rows="3" required></textarea>
                                </div>
                                <div style="margin-top: 15px;">
                                    <h5>ITENS DA OT:</h5>
                                    <div id="ot-items">
                                        <div class="ot-item" style="display: grid; grid-template-columns: 3fr 1fr 1fr auto; gap: 10px; margin-bottom: 10px;">
                                            <select class="form-input ot-item-select">
                                                <option value="">SELECIONE UM ITEM</option>
                                                <!-- Items will be loaded dynamically -->
                                            </select>
                                            <input type="number" class="form-input ot-item-quantity" placeholder="QTD" min="1" required>
                                            <input type="text" class="form-input ot-item-location" placeholder="LOCAL" readonly>
                                            <button type="button" class="btn btn-danger remove-ot-item" style="padding: 5px 10px;">
                                                <i class="fas fa-times"></i>
                                            </button>
                                        </div>
                                    </div>
                                    <button type="button" id="add-ot-item" class="btn" style="margin-top: 10px;">
                                        <i class="fas fa-plus"></i> ADICIONAR ITEM
                                    </button>
                                </div>
                                <button type="submit" class="btn btn-success" style="margin-top: 20px; width: 100%;">
                                    <i class="fas fa-save"></i> REGISTRAR OT
                                </button>
                            </form>
                        </div>
                        
                        <div class="admin-section">
                            <h4><i class="fas fa-history"></i> HISTÓRICO DE OTS</h4>
                            <div id="ot-history-container">
                                <!-- OT history will be loaded dynamically -->
                                <p>NENHUMA OT REGISTRADA.</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Dashboard Modal -->
        <div id="dashboard-modal" class="dashboard-modal">
            <div class="dashboard-content">
                <span class="close-modal">&times;</span>
                <div class="modal-header">
                    <h2>ANÁLISE DE DADOS DO ESTOQUE</h2>
                </div>
                <div class="dashboard-grid">
                    <div class="dashboard-card">
                        <h4><i class="fas fa-tags"></i> ITENS POR CATEGORIA</h4>
                        <div id="category-analytics" class="dashboard-stats">
                            <!-- Category analytics will be loaded dynamically -->
                        </div>
                        <div class="dashboard-chart">
                            <div class="chart-label">TOTAL DE ITENS: <span id="total-category-items">0</span></div>
                            <div class="chart-total" id="category-chart-total">0</div>
                        </div>
                    </div>
                </div>
                <div style="margin-top: 20px;">
                    <button id="export-analytics" class="btn" style="width: 100%;">
                        <i class="fas fa-file-export"></i> EXPORTAR RELATÓRIO
                    </button>
                </div>
            </div>
        </div>
        
        <!-- Print Container (hidden until print) -->
        <div id="print-container" class="print-container" style="display: none;">
            <h2 style="text-align: center; margin-bottom: 20px;">ESTOQUE CENTRAL</h2>
            <table class="print-table">
                <thead>
                    <tr>
                        <th>CÓDIGO</th>
                        <th>NOME DO ITEM</th>
                        <th>QUANTIDADE</th>
                        <th>LOCALIZAÇÃO</th>
                    </tr>
                </thead>
                <tbody id="print-items">
                    <!-- Items will be loaded dynamically -->
                </tbody>
            </table>
        </div>
        
        <!-- Mobile Navigation -->
        <div class="mobile-nav">
            <button id="mobile-menu-btn">
                <i class="fas fa-bars"></i>
            </button>
            <div class="mobile-menu">
                <button id="mobile-search-btn">
                    <i class="fas fa-search"></i> BUSCAR
                </button>
                <button id="mobile-admin-btn">
                    <i class="fas fa-cog"></i> ADMIN
                </button>
                <button id="mobile-print-btn">
                    <i class="fas fa-print"></i> IMPRIMIR
                </button>
            </div>
        </div>
        
        <div id="overlay"></div>
        
        <footer>
            <p>SISTEMA DE GERENCIAMENTO DE ESTOQUE CIMCOP &copy; 2023</p>
            <p>DESDE 1957. E ATÉ QUANDO HOUVER UM SONHO A CONSTRUIR</p>
        </footer>
    </div>
    
    <script>
        // Sample inventory data with localStorage persistence
        let inventoryData = JSON.parse(localStorage.getItem('inventoryData')) || [
            { id: 10001, name: "CAPACETE DE SEGURANÇA", quantity: 15, location: "A1", description: "CAPACETE BRANCO COM CARNEIRA", equivalencia: "-", aplicacao: "PROTEÇÃO", ca: "SIM", category: "420" },
            { id: 10002, name: "LUVAS DE PROTEÇÃO", quantity: 30, location: "A2", description: "LUVAS DE COURO TAMANHO G", equivalencia: "MODELO X", aplicacao: "PROTEÇÃO", ca: "SIM", category: "420" },
            { id: 20001, name: "MARTELO", quantity: 5, location: "B1", description: "MARTELO DE UNHA 27MM", equivalencia: "-", aplicacao: "CONSTRUÇÃO", ca: "NÃO", category: "140" },
            { id: 20002, name: "CHAVE DE FENDA", quantity: 8, location: "B2", description: "CHAVE DE FENDA 1/8 X 6", equivalencia: "-", aplicacao: "MANUTENÇÃO", ca: "NÃO", category: "140" },
            { id: 30001, name: "CIMENTO", quantity: 20, location: "C1", description: "SACO DE CIMENTO 50KG", equivalencia: "-", aplicacao: "CONSTRUÇÃO", ca: "NÃO", category: "440" },
            { id: 30002, name: "AREIA", quantity: 15, location: "C2", description: "AREIA FINA LAVADA", equivalencia: "-", aplicacao: "CONSTRUÇÃO", ca: "NÃO", category: "440" },
            { id: 40001, name: "BETONEIRA", quantity: 2, location: "D1", description: "BETONEIRA 400L", equivalencia: "-", aplicacao: "CONSTRUÇÃO", ca: "NÃO", category: "450" },
            { id: 40002, name: "FURADEIRA", quantity: 4, location: "D2", description: "FURADEIRA DE IMPACTO 750W", equivalencia: "MODELO Y", aplicacao: "CONSTRUÇÃO", ca: "NÃO", category: "450" }
        ];

        // Transfer history
        let transferHistory = JSON.parse(localStorage.getItem('transferHistory')) || [];

        // Receipt history
        let receiptHistory = JSON.parse(localStorage.getItem('receiptHistory')) || [];

        // OT history
        let otHistory = JSON.parse(localStorage.getItem('otHistory')) || [];

        // Activity log
        let activityLog = JSON.parse(localStorage.getItem('activityLog')) || [];

        // Save data to localStorage
        function saveData() {
            localStorage.setItem('inventoryData', JSON.stringify(inventoryData));
            localStorage.setItem('transferHistory', JSON.stringify(transferHistory));
            localStorage.setItem('receiptHistory', JSON.stringify(receiptHistory));
            localStorage.setItem('otHistory', JSON.stringify(otHistory));
            localStorage.setItem('activityLog', JSON.stringify(activityLog));
        }

        // Add to activity log
        function logActivity(action, details) {
            activityLog.push({
                timestamp: new Date().toISOString(),
                action: action.toUpperCase(),
                details: details.toUpperCase()
            });
            saveData();
        }

        // DOM Elements
        const divisionContainer = document.getElementById('division-container');
        const searchInput = document.getElementById('search-input');
        const searchButton = document.getElementById('search-button');
        const categoryFilter = document.getElementById('category-filter');
        const locationFilter = document.getElementById('location-filter');
        const analyticsButton = document.getElementById('analytics-button');
        const totalItemsElement = document.getElementById('total-items');
        const totalLocationsElement = document.getElementById('total-locations');
        const letterList = document.getElementById('letter-list');
        const adminButton = document.getElementById('admin-button');
        const adminModal = document.getElementById('admin-modal');
        const adminLogin = document.getElementById('admin-login');
        const adminPanel = document.getElementById('admin-panel');
        const adminPassword = document.getElementById('admin-password');
        const loginButton = document.getElementById('login-button');
        const loginError = document.getElementById('login-error');
        const closeModalButtons = document.querySelectorAll('.close-modal');
        const itemModal = document.getElementById('item-modal');
        const modalItemName = document.getElementById('modal-item-name');
        const modalDetails = document.getElementById('modal-details');
        const mobileMenuBtn = document.getElementById('mobile-menu-btn');
        const mobileMenu = document.querySelector('.mobile-menu');
        const overlay = document.getElementById('overlay');
        const tabButtons = document.querySelectorAll('.tab-button');
        const adminPanelContents = document.querySelectorAll('.admin-panel-content');
        const importExcelButton = document.getElementById('import-excel-button');
        const downloadTemplateButton = document.getElementById('download-template-button');
        const exportExcelButton = document.getElementById('export-excel-button');
        const printButton = document.getElementById('print-button');
        const clearDataButton = document.getElementById('clear-data-button');
        const resetSettingsButton = document.getElementById('reset-settings-button');
        const addItemForm = document.getElementById('add-item-form');
        const transferForm = document.getElementById('transfer-form');
        const receiptForm = document.getElementById('receipt-form');
        const otForm = document.getElementById('ot-form');
        const transferItemSelect = document.getElementById('transfer-item');
        const receiptItemSelect = document.getElementById('receipt-item');
        const otItemSelect = document.querySelector('.ot-item-select');
        const printContainer = document.getElementById('print-container');
        const printItems = document.getElementById('print-items');
        const dashboardModal = document.getElementById('dashboard-modal');
        const categoryAnalytics = document.getElementById('category-analytics');
        const exportAnalytics = document.getElementById('export-analytics');
        const totalCategoryItems = document.getElementById('total-category-items');
        const categoryChartTotal = document.getElementById('category-chart-total');
        const listViewAllBtn = document.getElementById('list-view-all-btn');
        
        // Current filter state
        let currentFilter = {
            letter: 'all',
            search: '',
            category: 'all',
            location: 'all'
        };

        // Initialize the application
        function init() {
            updateStats(currentFilter);
            generateLetterList();
            loadFilters();
            renderInventory(currentFilter);
            setupEventListeners();
        }
        
        // Update statistics based on current filter
        function updateStats(filter = {}) {
            let filteredItems = [...inventoryData];
            
            if (filter.search) {
                const searchTerm = filter.search.toLowerCase();
                filteredItems = filteredItems.filter(item => 
                    item.id.toString().includes(searchTerm) || 
                    item.name.toLowerCase().includes(searchTerm) || 
                    (item.description && item.description.toLowerCase().includes(searchTerm))
                );
            }
            
            if (filter.category && filter.category !== 'all') {
                filteredItems = filteredItems.filter(item => (item.category || String(item.id).substring(0, 3)) === filter.category);
            }
            
            if (filter.location && filter.location !== 'all') {
                filteredItems = filteredItems.filter(item => item.location === filter.location);
            }
            
            if (filter.letter && filter.letter !== 'all') {
                filteredItems = filteredItems.filter(item => item.location.charAt(0) === filter.letter);
            }
            
            totalItemsElement.textContent = filteredItems.length;
            const uniqueLocations = [...new Set(filteredItems.map(item => item.location))];
            totalLocationsElement.textContent = uniqueLocations.length;
        }
        
        // Generate letter list for filtering
function generateLetterList() {
    letterList.innerHTML = '<div class="letter-item active" data-letter="all"><i class="fas fa-box"></i></div>';
    
    const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
    const itemsByLetter = {};
    
    inventoryData.forEach(item => {
        const firstLetter = item.location.charAt(0);
        itemsByLetter[firstLetter] = (itemsByLetter[firstLetter] || 0) + 1;
    });
    
    letters.forEach(letter => {
        const hasItems = itemsByLetter[letter] > 0;
        const letterItem = document.createElement('div');
        letterItem.className = `letter-item ${hasItems ? '' : 'disabled'}`;
        letterItem.dataset.letter = letter;
        
        if (hasItems) {
            letterItem.innerHTML = `${letter}<span class="letter-indicator"></span>`;
            letterItem.addEventListener('click', () => filterByLetter(letter));
        } else {
            letterItem.textContent = letter;
        }
        
        letterList.appendChild(letterItem);
    });

    // Adiciona o event listener para o botão "All"
    const allButton = letterList.querySelector('[data-letter="all"]');
    allButton.addEventListener('click', () => filterByLetter('all'));
}
        
        // Filter by letter
        function filterByLetter(letter) {
            currentFilter.letter = letter;
            
            // Update active state
            document.querySelectorAll('.letter-item').forEach(item => {
                item.classList.remove('active');
            });
            
            const selectedItem = document.querySelector(`.letter-item[data-letter="${letter}"]`);
            if (selectedItem) {
                selectedItem.classList.add('active');
            }
            
            renderInventory(currentFilter);
            updateStats(currentFilter);
        }
        
        // Load filter options
        function loadFilters() {
            // Load categories
            const categories = [...new Set(inventoryData.map(item => item.category || String(item.id).substring(0, 3)))];
            categoryFilter.innerHTML = '<option value="all">TODAS AS CATEGORIAS</option>';
            
            categories.sort().forEach(category => {
                const option = document.createElement('option');
                option.value = category;
                option.textContent = getCategoryName(category);
                categoryFilter.appendChild(option);
            });
            
            // Load locations
            const locations = [...new Set(inventoryData.map(item => item.location))];
            locationFilter.innerHTML = '<option value="all">TODAS AS LOCALIZAÇÕES</option>';
            
            locations.sort().forEach(location => {
                const option = document.createElement('option');
                option.value = location;
                option.textContent = location;
                locationFilter.appendChild(option);
            });
        }
        
        // Get category name based on ID
        function getCategoryName(categoryId) {
            const categories = {
                "110": "110 - COMBUSTIVEIS",
                "120": "120 - CORREIAS, POLIAS E TENSORES",
                "140": "140 - FERRAMENTAS",
                "150": "150 - FILTROS",
                "160": "160 - LUBRIFICANTES",
                "170": "170 - PECAS P/ SISTEMA HID. E PNEUM.",
                "180": "180 - MATERIAL DE DESGASTE RAPIDO",
                "190": "190 - MATERIAIS ELETRICOS",
                "200": "200 - MATERIAIS DE CONSUMO/OFICINA",
                "220": "220 - MATERIAL RODANTE",
                "230": "230 - PNEUS E ASSOCIADOS",
                "240": "240 - PORCAS, PARAFUSOS E ARRUELAS",
                "250": "250 - PECAS P/ MANCAIS E ARTICULACOES",
                "270": "270 - SERVICOS DE TERCEIROS",
                "280": "280 - PECAS P/ EQUIPAMENTOS",
                "400": "400 - MATERIAL DE EXPEDIENTE/ESCRIT.",
                "420": "420 - EPI MATERIAIS DE SEGURANCA (INFORMAR CA)",
                "440": "440 - MATERIAL DE CONSTRUCAO",
                "450": "450 - BENS DE NATUREZA PERMANENTE"
            };
            
            return categories[categoryId] || `CATEGORIA ${categoryId}`;
        }
        
        // Render inventory based on filters
        function renderInventory(filters = {}) {
            divisionContainer.innerHTML = '';
            
            // Apply filters
            let filteredItems = [...inventoryData];
            
            if (filters.search) {
                const searchTerm = filters.search.toLowerCase();
                filteredItems = filteredItems.filter(item => 
                    item.id.toString().includes(searchTerm) || 
                    item.name.toLowerCase().includes(searchTerm) || 
                    (item.description && item.description.toLowerCase().includes(searchTerm))
                );
            }
            
            if (filters.category && filters.category !== 'all') {
                filteredItems = filteredItems.filter(item => (item.category || String(item.id).substring(0, 3)) === filters.category);
            }
            
            if (filters.location && filters.location !== 'all') {
                filteredItems = filteredItems.filter(item => item.location === filters.location);
            }
            
            if (filters.letter && filters.letter !== 'all') {
                filteredItems = filteredItems.filter(item => item.location.charAt(0) === filters.letter);
            }
            
            // Group items by division (first letter of location)
            const itemsByDivision = {};
            
            filteredItems.forEach(item => {
                const division = item.location.charAt(0);
                if (!itemsByDivision[division]) {
                    itemsByDivision[division] = [];
                }
                itemsByDivision[division].push(item);
            });
            
            // Create division cards
            Object.keys(itemsByDivision).sort().forEach(division => {
                const items = itemsByDivision[division];
                createDivisionCard(division, items);
            });
            
            // Show message if no items found
            if (Object.keys(itemsByDivision).length === 0) {
                divisionContainer.innerHTML = `
                    <div class="admin-section" style="text-align: center; padding: 40px 20px;">
                        <i class="fas fa-search" style="font-size: 3rem; color: var(--secondary-color); margin-bottom: 20px;"></i>
                        <h3>NENHUM ITEM ENCONTRADO</h3>
                        <p>TENTE AJUSTAR OS FILTROS OU TERMOS DE BUSCA.</p>
                    </div>
                `;
            }
        }
        
        // Create division card
        function createDivisionCard(division, items) {
            const card = document.createElement('div');
            card.className = 'division-card expanded';
            
            const subLocations = [...new Set(items.map(item => item.location))].sort();
            const categories = [...new Set(items.map(item => item.category || String(item.id).substring(0, 3)))].sort();
            
            card.innerHTML = `
                <div class="division-header">
                    <h3><i class="fas fa-map-marker-alt"></i> ${division}</h3>
                    <span>${items.length} ITENS</span>
                </div>
                <div class="division-content">
                    <div class="card-filters">
                        <div class="filter-group">
                            <label>SUB-LOCALIZAÇÃO:</label>
                            <select class="sublocation-filter">
                                <option value="all">TODAS</option>
                                ${subLocations.map(loc => `<option value="${loc}">${loc}</option>`).join('')}
                            </select>
                        </div>
                        <div class="filter-group">
                            <label>CATEGORIA:</label>
                            <select class="category-filter">
                                <option value="all">TODAS</option>
                                ${categories.map(cat => `<option value="${cat}">${getCategoryName(cat)}</option>`).join('')}
                            </select>
                        </div>
                        <button class="btn list-view-btn" data-letter="${division}">
                            <i class="fas fa-list"></i> VISUALIZAR EM LISTA
                        </button>
                    </div>
                    <div class="division-items">
                        ${items.map(item => createItemCard(item)).join('')}
                    </div>
                </div>
            `;
            
            divisionContainer.appendChild(card);
            
            // Add event listeners
            const header = card.querySelector('.division-header');
            header.addEventListener('click', () => {
                card.classList.toggle('expanded');
            });
            
            // Add filter functionality
            const sublocationFilter = card.querySelector('.sublocation-filter');
            const categoryFilter = card.querySelector('.category-filter');
            
            const applyFilters = () => {
                const sublocation = sublocationFilter.value;
                const category = categoryFilter.value;
                
                const itemElements = card.querySelectorAll('.division-item');
                
                itemElements.forEach(itemElement => {
                    const itemData = items.find(item => item.id.toString() === itemElement.dataset.id);
                    
                    let visible = true;
                    
                    if (sublocation !== 'all' && itemData.location !== sublocation) {
                        visible = false;
                    }
                    
                    if (category !== 'all' && (itemData.category || String(itemData.id).substring(0, 3)) !== category) {
                        visible = false;
                    }
                    
                    itemElement.style.display = visible ? 'block' : 'none';
                });
            };
            
            sublocationFilter.addEventListener('change', applyFilters);
            categoryFilter.addEventListener('change', applyFilters);
            
            // Add list view button functionality
            const listViewBtn = card.querySelector('.list-view-btn');
            listViewBtn.addEventListener('click', () => {
                showListView(division);
            });
        }
        
        // Create item card
        function createItemCard(item) {
            const categoryCode = item.category || String(item.id).substring(0, 3);
            const categoryName = getCategoryName(categoryCode);
            const quantityClass = item.quantity < 5 ? 'danger' : (item.quantity < 10 ? 'warning' : 'success');
            
            return `
                <div class="division-item" data-id="${item.id}" data-location="${item.location}">
                    <div class="item-header">
                        <span class="item-code">#${item.id}</span>
                        <span class="item-location">${item.location}</span>
                    </div>
                    <div class="item-category">${categoryName}</div>
                    <div class="item-name">${item.name}</div>
                    <div class="item-details">${item.description || 'SEM DESCRIÇÃO'}</div>
                    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;">
                        <span class="item-quantity ${quantityClass}">${item.quantity} UNID.</span>
                        <span style="font-size: 0.8rem; color: rgba(255, 255, 255, 0.6);">OFICINA CENTRAL (030)</span>
                    </div>
                    <button class="details-button" data-id="${item.id}">
                        <i class="fas fa-info-circle"></i> DETALHES
                    </button>
                </div>
            `;
        }
        
        // Show item details in modal
        function showItemDetails(itemId) {
            const item = inventoryData.find(item => item.id == itemId);
            
            if (!item) return;
            
            modalItemName.textContent = item.name;
            
            const categoryCode = item.category || String(item.id).substring(0, 3);
            const categoryName = getCategoryName(categoryCode);
            const quantityClass = item.quantity < 5 ? 'danger' : (item.quantity < 10 ? 'warning' : 'success');
            
            modalDetails.innerHTML = `
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px;">
                    <div>
                        <h4>INFORMAÇÕES BÁSICAS</h4>
                        <p><strong>CÓDIGO:</strong> #${item.id}</p>
                        <p><strong>CATEGORIA:</strong> ${categoryName}</p>
                        <p><strong>QUANTIDADE:</strong> <span class="item-quantity ${quantityClass}">${item.quantity} UNID.</span></p>
                        <p><strong>LOCALIZAÇÃO:</strong> ${item.location}</p>
                        <p><strong>OBRA:</strong> OFICINA CENTRAL (030)</p>
                    </div>
                    <div>
                        <h4>DETALHES TÉCNICOS</h4>
                        <p><strong>DESCRIÇÃO:</strong> ${item.description || 'SEM DESCRIÇÃO'}</p>
                        <p><strong>EQUIVALÊNCIA:</strong> ${item.equivalencia || 'N/A'}</p>
                        <p><strong>APLICAÇÃO:</strong> ${item.aplicacao || 'N/A'}</p>
                        <p><strong>CA EPI:</strong> ${item.ca || 'N/A'}</p>
                    </div>
                </div>
                
                <div style="display: flex; gap: 10px; margin-top: 20px;">
                    <button class="btn" onclick="editItem(${item.id})">
                        <i class="fas fa-edit"></i> EDITAR
                    </button>
                    <button class="btn btn-danger" onclick="confirmDeleteItem(${item.id})">
                        <i class="fas fa-trash-alt"></i> EXCLUIR
                    </button>
                </div>
                
                <div style="margin-top: 20px;">
                    <h4>HISTÓRICO DE MOVIMENTAÇÕES</h4>
                    <p>NENHUMA MOVIMENTAÇÃO REGISTRADA.</p>
                </div>
            `;
            
            itemModal.style.display = 'block';
            overlay.classList.add('active');
        }
        
        // Edit item
        function editItem(itemId) {
            const password = prompt('DIGITE A SENHA PARA EDITAR O ITEM:');
            if (password !== '0301957') {
                alert('SENHA INCORRETA!');
                return;
            }
            
            const item = inventoryData.find(item => item.id == itemId);
            if (!item) return;
            
            // Fill the form with item data
            document.getElementById('item-code').value = item.id;
            document.getElementById('item-name').value = item.name;
            document.getElementById('item-quantity').value = item.quantity;
            document.getElementById('item-location').value = item.location;
            document.getElementById('item-category').value = item.category || String(item.id).substring(0, 3);
            document.getElementById('item-description').value = item.description || '';
            document.getElementById('item-equivalencia').value = item.equivalencia || '';
            document.getElementById('item-aplicacao').value = item.aplicacao || '';
            document.getElementById('item-ca').value = item.ca || 'N/A';
            
            // Switch to inventory management tab
            document.querySelector('[data-tab="inventory-management"]').click();
            
            // Close the details modal
            itemModal.style.display = 'none';
            overlay.classList.remove('active');
            
            // Show admin modal if not already shown
            adminModal.style.display = 'block';
            adminLogin.style.display = 'none';
            adminPanel.style.display = 'block';
        }
        
        // Confirm delete item
        function confirmDeleteItem(itemId) {
            const item = inventoryData.find(item => item.id == itemId);
            if (!item) return;
            
            const password = prompt('DIGITE A SENHA PARA EXCLUIR O ITEM:');
            if (password !== '010301957') {
                alert('SENHA INCORRETA!');
                return;
            }
            
            const quantity = prompt(`QUANTIDADE A SER EXCLUÍDA (DISPONÍVEL: ${item.quantity}):`, item.quantity);
            if (quantity === null) return;
            
            const qty = parseInt(quantity);
            if (isNaN(qty) || qty <= 0 || qty > item.quantity) {
                alert('QUANTIDADE INVÁLIDA!');
                return;
            }
            
            if (confirm(`CONFIRMA EXCLUSÃO DE ${qty} UNIDADES DO ITEM ${item.name}?`)) {
                deleteItem(itemId, qty);
            }
        }
        
        // Delete item
        function deleteItem(itemId, quantity) {
            const index = inventoryData.findIndex(item => item.id == itemId);
            if (index !== -1) {
                const deletedItem = inventoryData[index];
                
                if (quantity >= deletedItem.quantity) {
                    // Remove completely if all quantity is being deleted
                    inventoryData.splice(index, 1);
                } else {
                    // Reduce quantity if partial deletion
                    inventoryData[index].quantity -= quantity;
                }
                
                saveData();
                renderInventory(currentFilter);
                updateStats(currentFilter);
                logActivity('delete', `ITEM EXCLUÍDO: ${deletedItem.name} (ID: ${deletedItem.id}), QUANTIDADE: ${quantity}`);
                
                // Close the modal if open
                itemModal.style.display = 'none';
                overlay.classList.remove('active');
            }
        }
        
        // Show list view for a letter
        function showListView(letter) {
            const items = inventoryData.filter(item => item.location.charAt(0) === letter);
            
            const modalTitle = `ITENS DA LETRA ${letter}`;
            const modalContent = `
                <table class="list-view-table">
                    <thead>
                        <tr>
                            <th>CÓDIGO</th>
                            <th>NOME DO ITEM</th>
                            <th>QUANTIDADE</th>
                            <th>LOCALIZAÇÃO</th>
                        </tr>
                    </thead>
                    <tbody>
                        ${items.map(item => `
                            <tr>
                                <td>${item.id}</td>
                                <td>${item.name}</td>
                                <td>${item.quantity}</td>
                                <td>${item.location}</td>
                            </tr>
                        `).join('')}
                    </tbody>
                </table>
            `;
            
            showGenericModal(modalTitle, modalContent);
        }
        
        // Show list view for all items with current filters
        function showAllListView() {
            let filteredItems = [...inventoryData];
            
            if (currentFilter.search) {
                const searchTerm = currentFilter.search.toLowerCase();
                filteredItems = filteredItems.filter(item => 
                    item.id.toString().includes(searchTerm) || 
                    item.name.toLowerCase().includes(searchTerm) || 
                    (item.description && item.description.toLowerCase().includes(searchTerm))
                );
            }
            
            if (currentFilter.category && currentFilter.category !== 'all') {
                filteredItems = filteredItems.filter(item => (item.category || String(item.id).substring(0, 3)) === currentFilter.category);
            }
            
            if (currentFilter.location && currentFilter.location !== 'all') {
                filteredItems = filteredItems.filter(item => item.location === currentFilter.location);
            }
            
            if (currentFilter.letter && currentFilter.letter !== 'all') {
                filteredItems = filteredItems.filter(item => item.location.charAt(0) === currentFilter.letter);
            }
            
            const modalTitle = `TODOS OS ITENS ${currentFilter.letter !== 'all' ? `DA LETRA ${currentFilter.letter}` : ''}`;
            const modalContent = `
                <table class="list-view-table">
                    <thead>
                        <tr>
                            <th>CÓDIGO</th>
                            <th>NOME DO ITEM</th>
                            <th>QUANTIDADE</th>
                            <th>LOCALIZAÇÃO</th>
                        </tr>
                    </thead>
                    <tbody>
                        ${filteredItems.map(item => `
                            <tr>
                                <td>${item.id}</td>
                                <td>${item.name}</td>
                                <td>${item.quantity}</td>
                                <td>${item.location}</td>
                            </tr>
                        `).join('')}
                    </tbody>
                </table>
            `;
            
            showGenericModal(modalTitle, modalContent);
        }
        
        // Show generic modal
        function showGenericModal(title, content) {
            const modal = document.createElement('div');
            modal.className = 'modal';
            modal.innerHTML = `
                <div class="modal-content">
                    <span class="close-modal">&times;</span>
                    <div class="modal-header">
                        <h2>${title}</h2>
                    </div>
                    <div class="modal-body">
                        ${content}
                    </div>
                </div>
            `;
            
            document.body.appendChild(modal);
            modal.style.display = 'block';
            overlay.classList.add('active');
            
            const closeBtn = modal.querySelector('.close-modal');
            closeBtn.addEventListener('click', () => {
                modal.style.display = 'none';
                overlay.classList.remove('active');
                document.body.removeChild(modal);
            });
        }
        
        // Prepare print view
        function preparePrintView() {
            printItems.innerHTML = '';
            
            inventoryData.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.id}</td>
                    <td>${item.name}</td>
                    <td>${item.quantity}</td>
                    <td>${item.location}</td>
                `;
                printItems.appendChild(row);
            });
            
            // Show the print container temporarily
            printContainer.style.display = 'block';
            window.print();
            printContainer.style.display = 'none';
        }
        
        // Export to Excel
        function exportToExcel() {
            // Prepare data for export
            const exportData = inventoryData.map(item => {
                const categoryCode = item.category || String(item.id).substring(0, 3);
                return {
                    'CÓDIGO': item.id,
                    'NOME': item.name,
                    'QUANTIDADE': item.quantity,
                    'LOCALIZAÇÃO': item.location,
                    'OBRA': '030 - OFICINA CENTRAL',
                    'CATEGORIA': getCategoryName(categoryCode),
                    'DESCRIÇÃO': item.description || '',
                    'EQUIVALÊNCIA': item.equivalencia || '',
                    'APLICAÇÃO': item.aplicacao || '',
                    'CA EPI': item.ca || 'N/A'
                };
            });
            
            // Create worksheet
            const ws = XLSX.utils.json_to_sheet(exportData);
            
            // Create workbook
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "ESTOQUE");
            
            // Export to file
            XLSX.writeFile(wb, "ESTOQUE_CIMCOP.XLSX");
        }
        
        // Download template
        function downloadTemplate() {
            // Create template data
            const templateData = [{
                'CÓDIGO': '',
                'NOME': '',
                'QUANTIDADE': '',
                'LOCALIZAÇÃO': '',
                'DESCRIÇÃO': '',
                'EQUIVALÊNCIA': '',
                'APLICAÇÃO': '',
                'CA EPI': ''
            }];
            
            // Create worksheet
            const ws = XLSX.utils.json_to_sheet(templateData);
            
            // Create workbook
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "MODELO");
            
            // Export to file
            XLSX.writeFile(wb, "MODELO_ESTOQUE_CIMCOP.XLSX");
        }
        
        // Import from Excel
        function importFromExcel(file) {
            const reader = new FileReader();
            
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                
                // Get first sheet
                const worksheet = workbook.Sheets[workbook.SheetNames[0]];
                const jsonData = XLSX.utils.sheet_to_json(worksheet);
                
                // Process imported data
                const newItems = [];
                
                jsonData.forEach(row => {
                    if (row.CÓDIGO && row.NOME && row.QUANTIDADE && row.LOCALIZAÇÃO) {
                        // Extract category from code (first 3 digits)
                        const category = String(row.CÓDIGO).substring(0, 3);
                        
                        // Check if item already exists
                        const existingItemIndex = inventoryData.findIndex(item => item.id == row.CÓDIGO);
                        
                        if (existingItemIndex !== -1) {
                            // Update existing item - SUM quantities
                            inventoryData[existingItemIndex].quantity += parseInt(row.QUANTIDADE) || 0;
                            inventoryData[existingItemIndex].location = row.LOCALIZAÇÃO.toUpperCase();
                            
                            if (row.DESCRIÇÃO) inventoryData[existingItemIndex].description = row.DESCRIÇÃO.toUpperCase();
                            if (row.EQUIVALÊNCIA) inventoryData[existingItemIndex].equivalencia = row.EQUIVALÊNCIA.toUpperCase();
                            if (row.APLICAÇÃO) inventoryData[existingItemIndex].aplicacao = row.APLICAÇÃO.toUpperCase();
                            if (row['CA EPI']) inventoryData[existingItemIndex].ca = row['CA EPI'].toUpperCase();
                        } else {
                            // Add new item
                            newItems.push({
                                id: row.CÓDIGO,
                                name: row.NOME.toUpperCase(),
                                quantity: row.QUANTIDADE,
                                location: row.LOCALIZAÇÃO.toUpperCase(),
                                description: (row.DESCRIÇÃO || '').toUpperCase(),
                                equivalencia: (row.EQUIVALÊNCIA || '').toUpperCase(),
                                aplicacao: (row.APLICAÇÃO || '').toUpperCase(),
                                ca: (row['CA EPI'] || 'N/A').toUpperCase(),
                                category: category
                            });
                        }
                    }
                });
                
                if (newItems.length > 0 || jsonData.length > 0) {
                    // Add new items to inventory
                    inventoryData = [...inventoryData, ...newItems];
                    saveData();
                    renderInventory(currentFilter);
                    updateStats(currentFilter);
                    generateLetterList();
                    loadFilters();
                    
                    const updatedCount = newItems.length;
                    const modifiedCount = jsonData.length - newItems.length;
                    
                    let message = '';
                    if (updatedCount > 0) message += `${updatedCount} ITENS IMPORTADOS COM SUCESSO! `;
                    if (modifiedCount > 0) message += `${modifiedCount} ITENS ATUALIZADOS!`;
                    
                    alert(message || 'NENHUM ITEM NOVO ADICIONADO, MAS ITENS EXISTENTES FORAM ATUALIZADOS.');
                    logActivity('import', `${updatedCount} ITENS IMPORTADOS E ${modifiedCount} ATUALIZADOS VIA EXCEL`);
                } else {
                    alert('NENHUM ITEM VÁLIDO ENCONTRADO NO ARQUIVO. VERIFIQUE SE AS COLUNAS OBRIGATÓRIAS (CÓDIGO, NOME, QUANTIDADE, LOCALIZAÇÃO) ESTÃO PREENCHIDAS.');
                }
            };
            
            reader.readAsArrayBuffer(file);
        }
        
        // Show dashboard
        function showDashboard() {
            // Get current filtered items
            let filteredItems = [...inventoryData];
            
            if (currentFilter.search) {
                const searchTerm = currentFilter.search.toLowerCase();
                filteredItems = filteredItems.filter(item => 
                    item.id.toString().includes(searchTerm) || 
                    item.name.toLowerCase().includes(searchTerm.toLowerCase()) || 
                    (item.description && item.description.toLowerCase().includes(searchTerm.toLowerCase()))
                );
            }
            
            if (currentFilter.category !== 'all') {
                filteredItems = filteredItems.filter(item => (item.category || String(item.id).substring(0, 3)) === currentFilter.category);
            }
            
            if (currentFilter.location !== 'all') {
                filteredItems = filteredItems.filter(item => item.location === currentFilter.location);
            }
            
            if (currentFilter.letter !== 'all') {
                filteredItems = filteredItems.filter(item => item.location.charAt(0) === currentFilter.letter);
            }
            
            // Calculate category stats
            const categoryStatsData = {};
            filteredItems.forEach(item => {
                const category = item.category || String(item.id).substring(0, 3);
                categoryStatsData[category] = (categoryStatsData[category] || 0) + 1;
            });
            
            // Update category stats
            categoryAnalytics.innerHTML = '';
            let categoryTotal = 0;
            Object.entries(categoryStatsData).sort().forEach(([cat, count]) => {
                categoryAnalytics.innerHTML += `
                    <div class="dashboard-stat">
                        <div class="dashboard-stat-value">${count}</div>
                        <div class="dashboard-stat-label">${getCategoryName(cat)}</div>
                    </div>
                `;
                categoryTotal += count;
            });
            
            // Update category chart
            document.getElementById('total-category-items').textContent = categoryTotal;
            document.getElementById('category-chart-total').textContent = categoryTotal;
            
            dashboardModal.style.display = 'block';
            overlay.classList.add('active');
        }
        
        // Setup event listeners with debounce for mobile performance
        function setupEventListeners() {
            // Search functionality
            searchButton.addEventListener('click', () => {
                currentFilter.search = searchInput.value.trim();
                renderInventory(currentFilter);
                updateStats(currentFilter);
            });
            
            searchInput.addEventListener('keyup', (e) => {
                if (e.key === 'Enter') {
                    currentFilter.search = searchInput.value.trim();
                    renderInventory(currentFilter);
                    updateStats(currentFilter);
                }
            });
            
            // Filter functionality
            categoryFilter.addEventListener('change', () => {
                currentFilter.category = categoryFilter.value;
                renderInventory(currentFilter);
                updateStats(currentFilter);
            });
            
            locationFilter.addEventListener('change', () => {
                currentFilter.location = locationFilter.value;
                renderInventory(currentFilter);
                updateStats(currentFilter);
            });
            
            // Analytics button
            analyticsButton.addEventListener('click', showDashboard);
            
            // Admin modal
            adminButton.addEventListener('click', () => {
                adminModal.style.display = 'block';
                overlay.classList.add('active');
                adminLogin.style.display = 'block';
                adminPanel.style.display = 'none';
                adminPassword.value = '';
            });
            
            // Login functionality
            loginButton.addEventListener('click', () => {
                const password = adminPassword.value;
                
                if (password === '0301957') {
                    adminLogin.style.display = 'none';
                    adminPanel.style.display = 'block';
                    loginError.style.display = 'none';
                    
                    // Load transfer items
                    transferItemSelect.innerHTML = '<option value="">SELECIONE UM ITEM</option>';
                    inventoryData.forEach(item => {
                        const option = document.createElement('option');
                        option.value = item.id;
                        option.textContent = `${item.id} - ${item.name}`;
                        transferItemSelect.appendChild(option);
                    });
                    
                    // Load receipt items
                    receiptItemSelect.innerHTML = '<option value="new">NOVO ITEM</option>';
                    inventoryData.forEach(item => {
                        const option = document.createElement('option');
                        option.value = item.id;
                        option.textContent = `${item.id} - ${item.name}`;
                        receiptItemSelect.appendChild(option);
                    });
                    
                    // Load OT items
                    const otSelect = document.querySelector('.ot-item-select');
                    otSelect.innerHTML = '<option value="">SELECIONE UM ITEM</option>';
                    inventoryData.forEach(item => {
                        const option = document.createElement('option');
                        option.value = item.id;
                        option.textContent = `${item.id} - ${item.name}`;
                        otSelect.appendChild(option);
                    });
                } else {
                    loginError.style.display = 'block';
                    adminPassword.value = '';
                }
            });
            
            // Close modals
            closeModalButtons.forEach(button => {
                button.addEventListener('click', () => {
                    adminModal.style.display = 'none';
                    itemModal.style.display = 'none';
                    dashboardModal.style.display = 'none';
                    overlay.classList.remove('active');
                });
            });
            
            // Close modal when clicking outside
            overlay.addEventListener('click', () => {
                adminModal.style.display = 'none';
                itemModal.style.display = 'none';
                dashboardModal.style.display = 'none';
                overlay.classList.remove('active');
                mobileMenu.classList.remove('active');
            });
            
            // Mobile menu with debounce for performance
            let lastTap = 0;
            mobileMenuBtn.addEventListener('click', (e) => {
                const now = new Date().getTime();
                const timesince = now - lastTap;
                
                if (timesince < 300 && timesince > 0) {
                    // Double tap - ignore
                    e.preventDefault();
                } else {
                    // Single tap
                    mobileMenu.classList.toggle('active');
                    overlay.classList.toggle('active');
                }
                
                lastTap = now;
            });
            
            // Admin tabs
            tabButtons.forEach(button => {
                button.addEventListener('click', () => {
                    const tab = button.dataset.tab;
                    
                    tabButtons.forEach(btn => btn.classList.remove('active'));
                    button.classList.add('active');
                    
                    adminPanelContents.forEach(content => {
                        content.style.display = 'none';
                    });
                    
                    document.getElementById(tab).style.display = 'block';
                });
            });
            
            // Transfer item select change
            transferItemSelect.addEventListener('change', () => {
                const itemId = transferItemSelect.value;
                const item = inventoryData.find(item => item.id == itemId);
                if (item) {
                    document.getElementById('transfer-from').value = item.location;
                }
            });
            
            // OT item select change
            document.addEventListener('change', (e) => {
                if (e.target.classList.contains('ot-item-select')) {
                    const itemId = e.target.value;
                    const item = inventoryData.find(item => item.id == itemId);
                    if (item) {
                        const locationInput = e.target.closest('.ot-item').querySelector('.ot-item-location');
                        locationInput.value = item.location;
                    }
                }
            });
            
            // Add OT item
            document.getElementById('add-ot-item').addEventListener('click', () => {
                const otItems = document.getElementById('ot-items');
                const newItem = document.createElement('div');
                newItem.className = 'ot-item';
                newItem.style.display = 'grid';
                newItem.style.gridTemplateColumns = '3fr 1fr 1fr auto';
                newItem.style.gap = '10px';
                newItem.style.marginBottom = '10px';
                
                const select = document.createElement('select');
                select.className = 'form-input ot-item-select';
                select.innerHTML = '<option value="">SELECIONE UM ITEM</option>';
                inventoryData.forEach(item => {
                    const option = document.createElement('option');
                    option.value = item.id;
                    option.textContent = `${item.id} - ${item.name}`;
                    select.appendChild(option);
                });
                
                const quantityInput = document.createElement('input');
                quantityInput.type = 'number';
                quantityInput.className = 'form-input ot-item-quantity';
                quantityInput.placeholder = 'QTD';
                quantityInput.min = '1';
                quantityInput.required = true;
                
                const locationInput = document.createElement('input');
                locationInput.type = 'text';
                locationInput.className = 'form-input ot-item-location';
                locationInput.placeholder = 'LOCAL';
                locationInput.readOnly = true;
                
                const removeButton = document.createElement('button');
                removeButton.type = 'button';
                removeButton.className = 'btn btn-danger remove-ot-item';
                removeButton.style.padding = '5px 10px';
                removeButton.innerHTML = '<i class="fas fa-times"></i>';
                removeButton.addEventListener('click', () => {
                    otItems.removeChild(newItem);
                });
                
                newItem.appendChild(select);
                newItem.appendChild(quantityInput);
                newItem.appendChild(locationInput);
                newItem.appendChild(removeButton);
                
                otItems.appendChild(newItem);
            });
            
            // Add item form
            addItemForm.addEventListener('submit', (e) => {
                e.preventDefault();
                
                const id = parseInt(document.getElementById('item-code').value);
                const name = document.getElementById('item-name').value.toUpperCase();
                const quantity = parseInt(document.getElementById('item-quantity').value);
                const location = document.getElementById('item-location').value.toUpperCase();
                const category = document.getElementById('item-category').value;
                const description = document.getElementById('item-description').value.toUpperCase();
                const equivalencia = document.getElementById('item-equivalencia').value.toUpperCase();
                const aplicacao = document.getElementById('item-aplicacao').value.toUpperCase();
                const ca = document.getElementById('item-ca').value.toUpperCase();
                
                // Check if item already exists
                const existingIndex = inventoryData.findIndex(item => item.id === id);
                
                const newItem = {
                    id,
                    name,
                    quantity,
                    location,
                    category,
                    description,
                    equivalencia,
                    aplicacao,
                    ca
                };
                
                if (existingIndex !== -1) {
                    // Update existing item
                    inventoryData[existingIndex] = newItem;
                    logActivity('edit', `ITEM ATUALIZADO: ${name} (ID: ${id})`);
                } else {
                    // Add new item
                    inventoryData.push(newItem);
                    logActivity('add', `NOVO ITEM ADICIONADO: ${name} (ID: ${id})`);
                }
                
                saveData();
                renderInventory(currentFilter);
                updateStats(currentFilter);
                generateLetterList();
                loadFilters();
                
                // Reset form
                addItemForm.reset();
                
                alert('ITEM SALVO COM SUCESSO!');
            });
            
            // Transfer form
            transferForm.addEventListener('submit', (e) => {
                e.preventDefault();
                
                const itemId = parseInt(transferItemSelect.value);
                const fromLocation = document.getElementById('transfer-from').value;
                const toLocation = document.getElementById('transfer-to').value.toUpperCase();
                const quantity = parseInt(document.getElementById('transfer-quantity').value);
                const date = document.getElementById('transfer-date').value;
                const reason = document.getElementById('transfer-reason').value.toUpperCase();
                
                // Find the item
                const itemIndex = inventoryData.findIndex(item => item.id === itemId);
                if (itemIndex === -1) {
                    alert('ITEM NÃO ENCONTRADO!');
                    return;
                }
                
                const item = inventoryData[itemIndex];
                
                // Check quantity
                if (quantity > item.quantity) {
                    alert('QUANTIDADE SOLICITADA MAIOR QUE A DISPONÍVEL NO ESTOQUE!');
                    return;
                }
                
                // Update item location and quantity
                item.location = toLocation;
                item.quantity -= quantity;
                
                // Add to transfer history
                transferHistory.push({
                    itemId,
                    itemName: item.name,
                    fromLocation,
                    toLocation,
                    quantity,
                    date,
                    reason,
                    timestamp: new Date().toISOString()
                });
                
                saveData();
                renderInventory(currentFilter);
                updateStats(currentFilter);
                
                // Reset form
                transferForm.reset();
                
                alert('TRANSFERÊNCIA REGISTRADA COM SUCESSO!');
                logActivity('transfer', `ITEM TRANSFERIDO: ${item.name} (ID: ${itemId}) DE ${fromLocation} PARA ${toLocation}`);
            });
            
            // Receipt form
            receiptForm.addEventListener('submit', (e) => {
                e.preventDefault();
                
                const itemId = receiptItemSelect.value === 'new' ? null : parseInt(receiptItemSelect.value);
                const quantity = parseInt(document.getElementById('receipt-quantity').value);
                const date = document.getElementById('receipt-date').value;
                const supplier = document.getElementById('receipt-supplier').value.toUpperCase();
                const invoice = document.getElementById('receipt-invoice').value.toUpperCase();
                const location = document.getElementById('receipt-location').value.toUpperCase();
                const notes = document.getElementById('receipt-notes').value.toUpperCase();
                
                if (itemId === null) {
                    // New item - should be added via inventory management first
                    alert('POR FAVOR, ADICIONE O NOVO ITEM PRIMEIRO NA SEÇÃO DE GERENCIAR ESTOQUE.');
                    return;
                }
                
                // Find the item
                const itemIndex = inventoryData.findIndex(item => item.id === itemId);
                if (itemIndex === -1) {
                    alert('ITEM NÃO ENCONTRADO!');
                    return;
                }
                
                // Update item quantity
                inventoryData[itemIndex].quantity += quantity;
                
                // Add to receipt history
                receiptHistory.push({
                    itemId,
                    itemName: inventoryData[itemIndex].name,
                    quantity,
                    date,
                    supplier,
                    invoice,
                    location,
                    notes,
                    timestamp: new Date().toISOString()
                });
                
                saveData();
                renderInventory(currentFilter);
                updateStats(currentFilter);
                
                // Reset form
                receiptForm.reset();
                receiptItemSelect.value = 'new';
                
                alert('RECEBIMENTO REGISTRADO COM SUCESSO!');
                logActivity('receipt', `RECEBIMENTO DE ITEM: ${inventoryData[itemIndex].name} (ID: ${itemId}), QUANTIDADE: ${quantity}`);
            });
            
            // OT form
            otForm.addEventListener('submit', (e) => {
                e.preventDefault();
                
                const otNumber = document.getElementById('ot-number').value.toUpperCase();
                const otDate = document.getElementById('ot-date').value;
                const requester = document.getElementById('ot-requester').value.toUpperCase();
                const department = document.getElementById('ot-department').value.toUpperCase();
                const description = document.getElementById('ot-description').value.toUpperCase();
                
                // Get OT items
                const otItems = [];
                const otItemElements = document.querySelectorAll('.ot-item');
                
                otItemElements.forEach(itemElement => {
                    const itemId = itemElement.querySelector('.ot-item-select').value;
                    const quantity = itemElement.querySelector('.ot-item-quantity').value;
                    const location = itemElement.querySelector('.ot-item-location').value;
                    
                    if (itemId && quantity && location) {
                        const item = inventoryData.find(item => item.id == itemId);
                        if (item) {
                            otItems.push({
                                itemId,
                                itemName: item.name,
                                quantity: parseInt(quantity),
                                location
                            });
                        }
                    }
                });
                
                if (otItems.length === 0) {
                    alert('ADICIONE PELO MENOS UM ITEM À OT!');
                    return;
                }
                
                // Add to OT history
                otHistory.push({
                    otNumber,
                    date: otDate,
                    requester,
                    department,
                    description,
                    items: otItems,
                    timestamp: new Date().toISOString()
                });
                
                saveData();
                
                // Reset form
                otForm.reset();
                document.getElementById('ot-items').innerHTML = `
                    <div class="ot-item" style="display: grid; grid-template-columns: 3fr 1fr 1fr auto; gap: 10px; margin-bottom: 10px;">
                        <select class="form-input ot-item-select">
                            <option value="">SELECIONE UM ITEM</option>
                            ${inventoryData.map(item => `<option value="${item.id}">${item.id} - ${item.name}</option>`).join('')}
                        </select>
                        <input type="number" class="form-input ot-item-quantity" placeholder="QTD" min="1" required>
                        <input type="text" class="form-input ot-item-location" placeholder="LOCAL" readonly>
                        <button type="button" class="btn btn-danger remove-ot-item" style="padding: 5px 10px;">
                            <i class="fas fa-times"></i>
                        </button>
                    </div>
                `;
                
                alert('OT REGISTRADA COM SUCESSO!');
                logActivity('ot', `NOVA OT REGISTRADA: ${otNumber}`);
            });
            
            // Excel import
            importExcelButton.addEventListener('click', () => {
                const fileInput = document.createElement('input');
                fileInput.type = 'file';
                fileInput.accept = '.xlsx,.xls';
                
                fileInput.addEventListener('change', (e) => {
                    const file = e.target.files[0];
                    if (file) {
                        importFromExcel(file);
                    }
                });
                
                fileInput.click();
            });
            
            // Download template
            downloadTemplateButton.addEventListener('click', downloadTemplate);
            
            // Excel export
            exportExcelButton.addEventListener('click', exportToExcel);
            
            // Print functionality
            printButton.addEventListener('click', preparePrintView);
            
            // Mobile print button
            document.getElementById('mobile-print-btn').addEventListener('click', () => {
                preparePrintView();
                mobileMenu.classList.remove('active');
                overlay.classList.remove('active');
            });
            
            // Mobile admin button
            document.getElementById('mobile-admin-btn').addEventListener('click', () => {
                adminModal.style.display = 'block';
                overlay.classList.add('active');
                adminLogin.style.display = 'block';
                adminPanel.style.display = 'none';
                adminPassword.value = '';
                mobileMenu.classList.remove('active');
            });
            
            // Mobile search button
            document.getElementById('mobile-search-btn').addEventListener('click', () => {
                searchInput.focus();
                mobileMenu.classList.remove('active');
                overlay.classList.remove('active');
            });
            
            // Clear data button
            clearDataButton.addEventListener('click', () => {
                const password = prompt('DIGITE A SENHA PARA LIMPAR TODOS OS DADOS:');
                if (password !== '010301957') {
                    alert('SENHA INCORRETA!');
                    return;
                }
                
                if (confirm('TEM CERTEZA QUE DESEJA LIMPAR TODOS OS DADOS? ESTA AÇÃO NÃO PODE SER DESFEITA.')) {
                    localStorage.clear();
                    inventoryData = [];
                    transferHistory = [];
                    receiptHistory = [];
                    otHistory = [];
                    activityLog = [];
                    init();
                    alert('TODOS OS DADOS FORAM LIMPOS!');
                    logActivity('system', 'TODOS OS DADOS FORAM LIMPOS');
                }
            });
            
            // Reset settings button
            resetSettingsButton.addEventListener('click', () => {
                const password = prompt('DIGITE A SENHA PARA REDEFINIR AS CONFIGURAÇÕES:');
                if (password !== '010301957') {
                    alert('SENHA INCORRETA!');
                    return;
                }
                
                if (confirm('TEM CERTEZA QUE DESEJA REDEFINIR TODAS AS CONFIGURAÇÕES? SUAS PREFERÊNCIAS SERÃO PERDIDAS.')) {
                    localStorage.removeItem('theme');
                    localStorage.removeItem('fontSize');
                    alert('CONFIGURAÇÕES REDEFINIDAS!');
                    logActivity('system', 'CONFIGURAÇÕES REDEFINIDAS');
                }
            });
            
            // Export analytics
            exportAnalytics.addEventListener('click', exportToExcel);
            
            // List view all button
            listViewAllBtn.addEventListener('click', showAllListView);
            
            // Details buttons (delegated event listener)
            document.addEventListener('click', (e) => {
                if (e.target.classList.contains('details-button') || e.target.closest('.details-button')) {
                    const button = e.target.classList.contains('details-button') ? e.target : e.target.closest('.details-button');
                    const itemId = button.dataset.id;
                    showItemDetails(itemId);
                }
            });
        }
        
        // Initialize the application
        init();
    </script>
</body>
</html>
