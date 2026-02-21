<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    
    <!-- PWA Configuration -->
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Ma Cave">
    <meta name="application-name" content="Ma Cave à Vins">
    <meta name="theme-color" content="#d4af6a">
    <meta name="description" content="Gérez votre collection de vins et votre wishlist œnologique">
    
    <!-- Icônes PWA -->
    <link rel="icon" type="image/svg+xml" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect fill='%23d4af6a' width='100' height='100' rx='20'/%3E%3Ctext x='50' y='70' font-size='60' text-anchor='middle' fill='white'%3E🍷%3C/text%3E%3C/svg%3E">
    <link rel="apple-touch-icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 180 180'%3E%3Crect fill='%23d4af6a' width='180' height='180' rx='40'/%3E%3Ctext x='90' y='130' font-size='100' text-anchor='middle' fill='white'%3E🍷%3C/text%3E%3C/svg%3E">
    <link rel="manifest" href="#" id="manifest-placeholder">
    
    <title>Ma Cave à Vins V4 • IndexedDB</title>
    <link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
            -webkit-touch-callout: none;
        }

        body {
            font-family: 'Raleway', sans-serif;
            background: linear-gradient(135deg, #f5f5f0 0%, #e8e8e0 100%);
            min-height: 100vh;
            padding: 0;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            touch-action: pan-y;
            overscroll-behavior: none;
        }

        .container {
            max-width: 480px;
            margin: 0 auto;
            padding: 12px 16px 80px;
        }

        header {
            text-align: center;
            margin-bottom: 16px;
            padding-top: 8px;
        }

        h1 {
            font-size: 26px;
            font-weight: 700;
            color: #4a4a4a;
            margin-bottom: 3px;
            letter-spacing: 0.3px;
        }

        .subtitle {
            font-size: 13px;
            color: #8a8a8a;
            font-weight: 400;
        }

        .search-container {
            margin-bottom: 12px;
            position: relative;
        }

        .search-shortcuts {
            display: flex;
            gap: 6px;
            margin-bottom: 8px;
            flex-wrap: wrap;
        }

        .search-shortcut {
            padding: 5px 10px;
            background: rgba(212, 175, 106, 0.1);
            border: 1px solid #d4af6a;
            border-radius: 16px;
            font-size: 12px;
            font-weight: 600;
            color: #6a6a6a;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 4px;
        }

        .search-shortcut:active {
            transform: scale(0.95);
            background: rgba(212, 175, 106, 0.2);
        }

        .search-suggestions {
            position: absolute;
            top: 100%;
            left: 0;
            right: 0;
            background: white;
            border: 2px solid #d4af6a;
            border-radius: 12px;
            margin-top: 4px;
            max-height: 250px;
            overflow-y: auto;
            z-index: 100;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }

        .search-suggestion-item {
            padding: 12px 16px;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid #f0f0f0;
            transition: background 0.2s;
        }

        .search-suggestion-item:last-child {
            border-bottom: none;
        }

        .search-suggestion-item:active {
            background: rgba(212, 175, 106, 0.1);
        }

        .search-suggestion-text {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 14px;
            font-weight: 500;
            color: #4a4a4a;
        }

        .search-suggestion-count {
            font-size: 12px;
            font-weight: 600;
            color: #d4af6a;
            background: rgba(212, 175, 106, 0.15);
            padding: 2px 8px;
            border-radius: 10px;
        }

        body.dark-mode .search-shortcut {
            background: rgba(212, 175, 106, 0.1);
            color: #e8e8e8;
        }

        body.dark-mode .search-suggestions {
            background: #2a2a2a;
            border-color: #d4af6a;
        }

        body.dark-mode .search-suggestion-item {
            border-bottom-color: #3a3a3a;
        }

        body.dark-mode .search-suggestion-text {
            color: #e8e8e8;
        }

        .search-input {
            width: 100%;
            padding: 12px 40px 12px 16px;
            border: 2px solid #e8e8e0;
            border-radius: 12px;
            background: white;
            font-family: 'Raleway', sans-serif;
            font-size: 15px;
            color: #4a4a4a;
            transition: all 0.2s;
            -webkit-appearance: none;
            appearance: none;
        }

        .search-input:focus {
            outline: none;
            border-color: #d4af6a;
            box-shadow: 0 0 0 3px rgba(212, 175, 106, 0.1);
        }

        .search-input::placeholder {
            color: #8a8a8a;
        }

        .search-icon {
            position: absolute;
            right: 14px;
            top: 50%;
            transform: translateY(-50%);
            font-size: 18px;
            color: #8a8a8a;
            pointer-events: none;
        }

        .clear-search {
            position: absolute;
            right: 14px;
            top: 50%;
            transform: translateY(-50%);
            background: #d4af6a;
            border: none;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            color: white;
            font-size: 14px;
            font-weight: 600;
        }

        .filter-container {
            margin-bottom: 20px;
            position: relative;
        }

        .filter-select {
            width: 100%;
            padding: 12px 40px 12px 16px;
            border: 2px solid #e8e8e0;
            border-radius: 12px;
            background: white;
            font-family: 'Raleway', sans-serif;
            font-size: 15px;
            font-weight: 500;
            color: #4a4a4a;
            cursor: pointer;
            appearance: none;
            -webkit-appearance: none;
            -moz-appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='%23d4af6a' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 12px center;
            background-size: 20px;
            transition: all 0.2s;
        }

        .filter-select:focus {
            outline: none;
            border-color: #d4af6a;
            box-shadow: 0 0 0 3px rgba(212, 175, 106, 0.1);
        }

        .filter-select option {
            padding: 12px;
            font-size: 15px;
        }

        .filters-toolbar {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
        }

        .filter-button, .sort-button {
            flex: 1;
            padding: 9px 12px;
            border: 2px solid #e8e8e0;
            border-radius: 10px;
            background: white;
            font-family: 'Raleway', sans-serif;
            font-size: 14px;
            font-weight: 600;
            color: #4a4a4a;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
            position: relative;
        }

        .filter-button:active, .sort-button:active {
            transform: scale(0.98);
        }

        .filter-button.active {
            border-color: #d4af6a;
            background: rgba(212, 175, 106, 0.1);
            color: #d4af6a;
        }

        .filter-badge {
            background: #d4af6a;
            color: white;
            border-radius: 10px;
            padding: 2px 6px;
            font-size: 11px;
            font-weight: 700;
            min-width: 18px;
            text-align: center;
        }

        .filters-panel {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
            background: white;
            border-radius: 12px;
            margin-bottom: 0;
        }

        .filters-panel.open {
            max-height: 800px;
            border: 2px solid #e8e8e0;
            padding: 12px;
        }

        .filter-group {
            margin-bottom: 10px;
        }

        .filter-group:last-child {
            margin-bottom: 0;
        }

        .filter-label {
            font-size: 12px;
            font-weight: 700;
            color: #6a6a6a;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: 6px;
            display: block;
        }

        .filter-select-small {
            width: 100%;
            padding: 8px 12px;
            border: 1px solid #e8e8e0;
            border-radius: 8px;
            background: white;
            font-family: 'Raleway', sans-serif;
            font-size: 13px;
            color: #4a4a4a;
            cursor: pointer;
        }

        .filter-select-small:focus {
            outline: none;
            border-color: #d4af6a;
        }

        .filter-actions {
            display: flex;
            gap: 8px;
            margin-top: 12px;
            padding-top: 12px;
            border-top: 1px solid #f0f0f0;
        }

        .filter-action-btn {
            flex: 1;
            padding: 8px;
            border: none;
            border-radius: 8px;
            font-family: 'Raleway', sans-serif;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
        }

        .filter-action-btn.reset {
            background: #f5f5f0;
            color: #6a6a6a;
        }

        .filter-action-btn.apply {
            background: #d4af6a;
            color: white;
        }

        .active-filters {
            display: flex;
            gap: 6px;
            flex-wrap: wrap;
            margin-bottom: 8px;
        }

        .filter-chip {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            padding: 4px 8px;
            background: rgba(212, 175, 106, 0.15);
            border: 1px solid #d4af6a;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
            color: #6a6a6a;
        }

        .filter-chip-remove {
            background: none;
            border: none;
            color: #d4af6a;
            font-size: 16px;
            cursor: pointer;
            padding: 0;
            width: 16px;
            height: 16px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: background 0.2s;
        }

        .filter-chip-remove:hover {
            background: rgba(212, 175, 106, 0.2);
        }

        .results-count {
            font-size: 13px;
            color: #8a8a8a;
            text-align: center;
            margin-bottom: 8px;
            font-weight: 500;
        }

        .fab-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.5);
            z-index: 999;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s, visibility 0.3s;
        }

        .fab-overlay.active {
            opacity: 1;
            visibility: visible;
        }

        .fab-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 1000;
        }

        .fab-main {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: linear-gradient(135deg, #d4af6a 0%, #c4a05a 100%);
            border: none;
            color: white;
            font-size: 32px;
            font-weight: 300;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(196, 160, 90, 0.4);
            transition: transform 0.3s;
            -webkit-user-select: none;
            user-select: none;
            touch-action: manipulation;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .fab-main.active {
            transform: rotate(45deg);
        }

        .fab-main:active {
            transform: scale(0.92);
        }

        .fab-main.active:active {
            transform: rotate(45deg) scale(0.92);
        }

        .fab-menu {
            position: absolute;
            bottom: 75px;
            right: 0;
            display: flex;
            flex-direction: column;
            gap: 12px;
            opacity: 0;
            visibility: hidden;
            transform: translateY(20px);
            transition: opacity 0.3s, visibility 0.3s, transform 0.3s;
        }

        .fab-menu.active {
            opacity: 1;
            visibility: visible;
            transform: translateY(0);
        }

        .fab-item {
            display: flex;
            align-items: center;
            justify-content: flex-end;
            gap: 12px;
            animation: fadeInUp 0.3s ease-out backwards;
        }

        .fab-item:nth-child(1) { animation-delay: 0.05s; }
        .fab-item:nth-child(2) { animation-delay: 0.1s; }
        .fab-item:nth-child(3) { animation-delay: 0.15s; }

        @keyframes fadeInUp {
            from {
                opacity: 0;
                transform: translateY(10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .fab-label {
            background: white;
            padding: 10px 16px;
            border-radius: 8px;
            font-family: 'Raleway', sans-serif;
            font-size: 14px;
            font-weight: 600;
            color: #2a2a2a;
            white-space: nowrap;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
        }

        .fab-button {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: none;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
            transition: transform 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            touch-action: manipulation;
        }

        .fab-button:active {
            transform: scale(0.9);
        }

        .fab-button.new-wine {
            background: linear-gradient(135deg, #d4af6a 0%, #c4a05a 100%);
            color: white;
        }

        .fab-button.import {
            background: #4a9eff;
            color: white;
        }

        .fab-button.export {
            background: #2a2a2a;
            color: white;
        }

        .csv-preview {
            margin: 16px 0;
            max-height: 300px;
            overflow-y: auto;
            border: 2px solid #e8e8e0;
            border-radius: 10px;
            padding: 12px;
            background: #fafafa;
        }

        .csv-preview-item {
            padding: 8px;
            border-bottom: 1px solid #e8e8e0;
            font-size: 13px;
        }

        .csv-preview-item:last-child {
            border-bottom: none;
        }

        .csv-stats {
            background: #fffef0;
            border: 2px solid #d4af6a;
            border-radius: 10px;
            padding: 12px;
            margin: 16px 0;
            font-size: 14px;
            color: #4a4a4a;
        }

        .csv-error {
            background: #ffe8e8;
            border: 2px solid #c44040;
            border-radius: 10px;
            padding: 12px;
            margin: 16px 0;
            font-size: 14px;
            color: #c44040;
        }

        .csv-drop-zone {
            border: 3px dashed #d4af6a;
            border-radius: 12px;
            padding: 40px 20px;
            text-align: center;
            background: #fffef0;
            cursor: pointer;
            transition: all 0.2s;
            margin: 16px 0;
        }

        .csv-drop-zone:hover {
            background: #fff8e0;
            border-color: #c4a05a;
        }

        .csv-drop-zone.drag-over {
            background: #fff0d0;
            border-color: #c4a05a;
            border-width: 4px;
        }

        .csv-import-options {
            display: flex;
            gap: 10px;
            margin: 16px 0;
        }

        .csv-option {
            flex: 1;
            padding: 12px;
            border: 2px solid #e8e8e0;
            border-radius: 10px;
            background: white;
            cursor: pointer;
            text-align: center;
            font-size: 13px;
            font-weight: 500;
            transition: all 0.2s;
        }

        .csv-option.selected {
            border-color: #d4af6a;
            background: #fffef0;
            font-weight: 600;
        }

        .wine-card {
            background: white;
            border-radius: 16px;
            padding: 0;
            margin-bottom: 10px;
            box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
            position: relative;
            transition: transform 0.2s, box-shadow 0.2s, opacity 0.2s;
            overflow: visible;
            -webkit-user-select: none;
            user-select: none;
            border: 2px solid transparent;
        }

        .wine-card.status-wishlist {
            border: 3px dashed #2a2a2a;
        }

        .wine-card.status-cellar {
            border: 3px solid #d4af6a;
        }

        .wine-card.selection-mode {
            padding-left: 0;
        }

        .wine-card.selected {
            transform: scale(0.97);
            opacity: 0.8;
            box-shadow: 0 0 0 3px #c44040;
        }

        .wine-card.drunk {
            opacity: 0.65;
        }

        .selection-indicator {
            position: absolute;
            top: 16px;
            left: 16px;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background: white;
            border: 2.5px solid #d4af6a;
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 25;
            font-size: 16px;
            transition: all 0.2s;
        }

        .wine-card.selection-mode .selection-indicator {
            display: flex;
        }

        .wine-card.selected .selection-indicator {
            background: #c44040;
            border-color: #c44040;
            color: white;
        }

        .wine-card-header {
            display: flex;
            gap: 0;
            paddi
