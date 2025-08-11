<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบจัดการสต๊อกเสื้อ</title>
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700;800&display=swap');
        body {
            font-family: 'Sarabun', sans-serif;
        }
        .gradient-bg {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        
        .card-shadow {
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .size-badge {
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            position: relative;
            overflow: hidden;
        }
        
        .size-badge::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
            transition: left 0.5s;
        }
        
        .size-badge:hover {
            transform: translateY(-4px) scale(1.05);
            box-shadow: 0 15px 30px rgba(0,0,0,0.3);
        }
        
        .size-badge:hover::before {
            left: 100%;
        }
        
        .withdraw-form {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            position: relative;
            overflow: hidden;
        }
        
        .withdraw-form::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);
            animation: float 6s ease-in-out infinite;
        }
        
        @keyframes float {
            0%, 100% { transform: translate(-50%, -50%) rotate(0deg); }
            50% { transform: translate(-30%, -70%) rotate(180deg); }
        }
        
        .stock-display {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            position: relative;
            overflow: hidden;
        }
        
        .stock-display::after {
            content: '';
            position: absolute;
            top: 0;
            right: 0;
            width: 100px;
            height: 100px;
            background: radial-gradient(circle, rgba(255,255,255,0.2) 0%, transparent 70%);
            animation: pulse 4s ease-in-out infinite;
        }
        
        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 0.7; }
            50% { transform: scale(1.2); opacity: 0.3; }
        }
        
        .male-section {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            border: 3px solid rgba(79, 172, 254, 0.5);
            position: relative;
            overflow: hidden;
        }
        
        .male-section::before {
            content: '👨';
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 2rem;
            opacity: 0.3;
            animation: bounce 2s infinite;
        }
        
        .female-section {
            background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
            border: 3px solid rgba(250, 112, 154, 0.5);
            position: relative;
            overflow: hidden;
        }
        
        .female-section::before {
            content: '👩';
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 2rem;
            opacity: 0.3;
            animation: bounce 2s infinite 0.5s;
        }
        
        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
            40% { transform: translateY(-10px); }
            60% { transform: translateY(-5px); }
        }
        
        .form-input {
            transition: all 0.3s ease;
            position: relative;
        }
        
        .form-input:focus {
            transform: translateY(-2px);
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
        }
        
        .submit-btn-enabled {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            position: relative;
            overflow: hidden;
        }
        
        .submit-btn-enabled::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
            transition: left 0.5s;
        }
        
        .submit-btn-enabled:hover::before {
            left: 100%;
        }
        
        .modal-backdrop {
            backdrop-filter: blur(10px);
            background: rgba(0, 0, 0, 0.6);
        }
        
        .modal-content {
            animation: modalSlideIn 0.3s ease-out;
        }
        
        @keyframes modalSlideIn {
            from {
                opacity: 0;
                transform: translateY(-50px) scale(0.9);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }
        
        .transaction-item {
            transition: all 0.3s ease;
            border-left: 4px solid transparent;
        }
        
        .transaction-item:hover {
            transform: translateX(5px);
            border-left-color: #667eea;
            background: rgba(102, 126, 234, 0.1);
        }
        
        .floating-icon {
            animation: floatIcon 3s ease-in-out infinite;
        }
        
        @keyframes floatIcon {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
        }
        
        .glow-effect {
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.3);
            animation: glow 2s ease-in-out infinite alternate;
        }
        
        @keyframes glow {
            from { box-shadow: 0 0 20px rgba(102, 126, 234, 0.3); }
            to { box-shadow: 0 0 30px rgba(102, 126, 234, 0.6); }
        }
    </style>
</head>
<body class="gradient-bg min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <!-- Header -->
        <div class="text-center mb-8 relative">
            <div class="floating-icon absolute top-0 left-1/4 text-4xl">✨</div>
            <div class="floating-icon absolute top-0 right-1/4 text-4xl" style="animation-delay: 1s;">🎯</div>
            <h1 class="text-4xl font-bold text-white mb-2 glow-effect">📦 ระบบจัดการสต๊อกเสื้อ</h1>
            <p class="text-white/90 text-lg font-medium">จัดการและเบิกเสื้อได้อย่างง่ายดาย</p>
            <div id="lastSavedInfo" class="text-white/70 text-sm mt-2">
                💾 บันทึกอัตโนมัติทุกครั้งที่มีการเปลี่ยนแปลง
            </div>
            <div id="firebaseStatus" class="text-white/70 text-xs mt-1">
                📱 โหมด localStorage (Firebase ยังไม่ได้ตั้งค่า)
            </div>
            <div class="mt-4">
                <button id="shareBtn" class="bg-white/20 hover:bg-white/30 text-white px-4 py-2 rounded-lg transition-all text-sm backdrop-blur-sm border border-white/30">
                    🔗 คัดลอกลิงก์แชร์ข้อมูลแบบเรียลไทม์
                </button>
                <div class="text-white/70 text-xs mt-2 text-center">
                    💡 ลิงก์อัปเดตทุก 10 วินาที เปิดใน Chrome คอมเครื่องไหนก็ได้ข้อมูลล่าสุดเสมอ
                </div>
            </div>
            <div class="mt-4 flex justify-center space-x-4">
                <div class="floating-icon text-2xl">👕</div>
                <div class="floating-icon text-2xl" style="animation-delay: 0.5s;">📊</div>
                <div class="floating-icon text-2xl" style="animation-delay: 1.5s;">🚀</div>
            </div>
        </div>

        <div class="grid lg:grid-cols-2 gap-4 lg:gap-8">
            <!-- Stock Display Section -->
            <div class="stock-display rounded-2xl p-4 sm:p-6 card-shadow">
                <div class="flex flex-col gap-4 mb-6">
                    <h2 class="text-xl sm:text-2xl font-bold text-gray-800 flex items-center text-center sm:text-left">
                        👕 จำนวนเสื้อคงเหลือ
                    </h2>
                    <div class="flex flex-col sm:flex-row gap-3">
                        <select id="categoryFilter" class="flex-1 px-4 py-3 rounded-lg bg-white/90 text-gray-800 font-medium focus:ring-2 focus:ring-purple-500 focus:outline-none text-sm sm:text-base">
                            <option value="all">แสดงทั้งหมด</option>
                            <option value="male">👨 ผู้ชาย</option>
                            <option value="female">👩 ผู้หญิง</option>
                            <option value="malePD">👨 ผู้ชายปัก PD.</option>
                            <option value="femalePD">👩 ผู้หญิงปัก PD.</option>
                            <option value="maleMT">👨 ผู้ชายปัก MT.</option>
                            <option value="maleENG">👨 ผู้ชายปัก ENG</option>
                            <option value="femaleENG">👩 ผู้หญิงปัก ENG</option>
                            <option value="maleTEC">👨 ผู้ชายปัก TEC.</option>
                            <option value="femaleTEC">👩 ผู้หญิงปัก TEC.</option>
                            <option value="jacket">🧥 JACKET</option>
                        </select>
                        <button id="editStockBtn" class="bg-purple-500 hover:bg-purple-600 text-white px-4 py-3 rounded-lg transition-all whitespace-nowrap text-sm sm:text-base">
                            ✏️ แก้ไขสต๊อก
                        </button>
                    </div>
                </div>
                
                <!-- Stock Display Container -->
                <div id="stockDisplayContainer">
                    <!-- All Categories View -->
                    <div id="allCategoriesView" class="grid lg:grid-cols-2 gap-6">
                        <!-- Male Side -->
                        <div class="male-section rounded-xl p-3 sm:p-4">
                            <h3 class="text-lg sm:text-xl font-bold text-blue-800 mb-4 text-center">👨 ผู้ชาย</h3>
                            
                            <!-- Male Regular -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-blue-700 mb-2 text-center">ไซต์ผู้ชาย</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="maleStockDisplay">
                                    <!-- Male stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Male PD -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-blue-700 mb-2 text-center">ไซต์ผู้ชายปัก PD.</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="malePDStockDisplay">
                                    <!-- Male PD stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Male MT -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-blue-700 mb-2 text-center">ไซต์ผู้ชายปัก MT.</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="maleMTStockDisplay">
                                    <!-- Male MT stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Male ENG -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-blue-700 mb-2 text-center">ไซต์ผู้ชายปัก ENG</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="maleENGStockDisplay">
                                    <!-- Male ENG stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Male TEC -->
                            <div>
                                <h4 class="text-sm sm:text-md font-semibold text-blue-700 mb-2 text-center">ไซต์ผู้ชายปัก TEC.</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="maleTECStockDisplay">
                                    <!-- Male TEC stock items will be populated by JavaScript -->
                                </div>
                            </div>
                        </div>
                        
                        <!-- Female Side -->
                        <div class="female-section rounded-xl p-3 sm:p-4">
                            <h3 class="text-lg sm:text-xl font-bold text-pink-800 mb-4 text-center">👩 ผู้หญิง</h3>
                            
                            <!-- Female Regular -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-pink-700 mb-2 text-center">ไซต์ผู้หญิง</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="femaleStockDisplay">
                                    <!-- Female stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Female PD -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-pink-700 mb-2 text-center">ไซต์ผู้หญิงปัก PD.</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="femalePDStockDisplay">
                                    <!-- Female PD stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Female ENG -->
                            <div class="mb-4">
                                <h4 class="text-sm sm:text-md font-semibold text-pink-700 mb-2 text-center">ไซต์ผู้หญิงปัก ENG</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="femaleENGStockDisplay">
                                    <!-- Female ENG stock items will be populated by JavaScript -->
                                </div>
                            </div>
                            
                            <!-- Female TEC -->
                            <div>
                                <h4 class="text-sm sm:text-md font-semibold text-pink-700 mb-2 text-center">ไซต์ผู้หญิงปัก TEC.</h4>
                                <div class="grid grid-cols-4 gap-1 sm:gap-2 justify-items-center" id="femaleTECStockDisplay">
                                    <!-- Female TEC stock items will be populated by JavaScript -->
                                </div>
                            </div>
                        </div>
                        
                        <!-- JACKET Section -->
                        <div class="lg:col-span-2 bg-gradient-to-br from-gray-600 to-gray-800 rounded-xl p-3 sm:p-4 border-3 border-gray-500/50 relative overflow-hidden">
                            <div class="absolute top-2 right-2 text-2xl opacity-30">🧥</div>
                            <h3 class="text-lg sm:text-xl font-bold text-gray-100 mb-4 text-center">🧥 JACKET</h3>
                            <div class="grid grid-cols-4 sm:grid-cols-8 gap-1 sm:gap-2 justify-items-center" id="jacketStockDisplay">
                                <!-- JACKET stock items will be populated by JavaScript -->
                            </div>
                        </div>
                    </div>
                    
                    <!-- Single Category Views -->
                    <div id="singleCategoryView" class="hidden">
                        <div id="singleCategoryContent" class="bg-white/20 rounded-xl p-4">
                            <!-- Single category content will be populated by JavaScript -->
                        </div>
                    </div>
                </div>
            </div>

            <!-- Withdraw Form Section -->
            <div class="withdraw-form rounded-2xl p-6 card-shadow">
                <h2 class="text-2xl font-bold text-white mb-6 flex items-center relative z-10">
                    <span class="floating-icon mr-2">📝</span> เบิกเสื้อ
                </h2>
                
                <form id="withdrawForm" class="space-y-6">
                    <div>
                        <label for="gender" class="block text-white font-medium mb-2">ประเภทเสื้อ</label>
                        <select id="gender" class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all">
                            <option value="">เลือกประเภทเสื้อ</option>
                            <option value="male">👨 ผู้ชาย</option>
                            <option value="female">👩 ผู้หญิง</option>
                            <option value="malePD">👨 ผู้ชายปัก PD.</option>
                            <option value="femalePD">👩 ผู้หญิงปัก PD.</option>
                            <option value="maleMT">👨 ผู้ชายปัก MT.</option>
                            <option value="maleENG">👨 ผู้ชายปัก ENG</option>
                            <option value="femaleENG">👩 ผู้หญิงปัก ENG</option>
                            <option value="maleTEC">👨 ผู้ชายปัก TEC.</option>
                            <option value="femaleTEC">👩 ผู้หญิงปัก TEC.</option>
                            <option value="jacket">🧥 JACKET</option>
                        </select>
                    </div>

                    <div>
                        <label for="size" class="block text-white font-medium mb-2">ไซต์</label>
                        <select id="size" class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all" disabled>
                            <option value="">เลือกไซต์</option>
                        </select>
                    </div>

                    <div>
                        <label for="withdrawer" class="block text-white font-medium mb-2">ผู้เบิก</label>
                        <input type="text" id="withdrawer" placeholder="กรอกชื่อผู้เบิก" 
                               class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all">
                    </div>

                    <div>
                        <label for="withdrawDate" class="block text-white font-medium mb-2">วันที่เบิก</label>
                        <input type="date" id="withdrawDate" 
                               class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all">
                    </div>

                    <div>
                        <label for="quantity" class="block text-white font-medium mb-2">จำนวน</label>
                        <input type="number" id="quantity" min="1" placeholder="กรอกจำนวนที่ต้องการเบิก" 
                               class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all">
                    </div>

                    <div>
                        <label for="notes" class="block text-white font-medium mb-2">หมายเหตุ <span class="text-white/70 text-sm">(ไม่บังคับ)</span></label>
                        <textarea id="notes" rows="3" placeholder="กรอกหมายเหตุเพิ่มเติม (ถ้ามี)" 
                                  class="form-input w-full px-4 py-3 rounded-xl border-0 bg-white/90 backdrop-blur-sm text-gray-800 font-medium focus:ring-4 focus:ring-white/30 focus:outline-none transition-all resize-none"></textarea>
                    </div>

                    <button type="submit" id="submitBtn" class="w-full bg-gray-400 text-gray-600 font-bold py-4 px-6 rounded-xl cursor-not-allowed transition-all duration-300 shadow-lg" disabled>
                        ✅ ยืนยันการเบิก
                    </button>
                </form>

                <!-- Result Message -->
                <div id="resultMessage" class="mt-6 p-4 rounded-xl hidden"></div>
            </div>
        </div>

        <!-- Edit Stock Modal -->
        <div id="editStockModal" class="modal-backdrop fixed inset-0 hidden z-50 flex items-center justify-center p-2 sm:p-4">
            <div class="modal-content bg-white rounded-2xl p-4 sm:p-6 max-w-4xl w-full max-h-[90vh] sm:max-h-[80vh] overflow-y-auto card-shadow">
                <div class="flex justify-between items-center mb-4 sm:mb-6">
                    <h3 class="text-lg sm:text-2xl font-bold text-gray-800">✏️ แก้ไขจำนวนสต๊อก</h3>
                    <button id="closeEditModal" class="text-gray-500 hover:text-gray-700 text-xl sm:text-2xl">×</button>
                </div>
                
                <div class="grid md:grid-cols-2 gap-6">
                    <!-- Male Side -->
                    <div class="bg-blue-50 p-4 rounded-lg border-2 border-blue-200">
                        <h4 class="text-lg sm:text-xl font-bold text-blue-800 mb-4 text-center">👨 ผู้ชาย</h4>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-blue-700 mb-3">ไซต์ผู้ชาย</h5>
                            <div id="maleEditInputs" class="space-y-2">
                                <!-- Male edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-blue-700 mb-3">ไซต์ผู้ชายปัก PD.</h5>
                            <div id="malePDEditInputs" class="space-y-2">
                                <!-- Male PD edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-blue-700 mb-3">ไซต์ผู้ชายปัก MT.</h5>
                            <div id="maleMTEditInputs" class="space-y-2">
                                <!-- Male MT edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-blue-700 mb-3">ไซต์ผู้ชายปัก ENG</h5>
                            <div id="maleENGEditInputs" class="space-y-2">
                                <!-- Male ENG edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div>
                            <h5 class="text-base font-semibold text-blue-700 mb-3">ไซต์ผู้ชายปัก TEC.</h5>
                            <div id="maleTECEditInputs" class="space-y-2">
                                <!-- Male TEC edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                    </div>
                    
                    <!-- Female Side -->
                    <div class="bg-pink-50 p-4 rounded-lg border-2 border-pink-200">
                        <h4 class="text-lg sm:text-xl font-bold text-pink-800 mb-4 text-center">👩 ผู้หญิง</h4>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-pink-700 mb-3">ไซต์ผู้หญิง</h5>
                            <div id="femaleEditInputs" class="space-y-2">
                                <!-- Female edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-pink-700 mb-3">ไซต์ผู้หญิงปัก PD.</h5>
                            <div id="femalePDEditInputs" class="space-y-2">
                                <!-- Female PD edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <h5 class="text-base font-semibold text-pink-700 mb-3">ไซต์ผู้หญิงปัก ENG</h5>
                            <div id="femaleENGEditInputs" class="space-y-2">
                                <!-- Female ENG edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                        
                        <div>
                            <h5 class="text-base font-semibold text-pink-700 mb-3">ไซต์ผู้หญิงปัก TEC.</h5>
                            <div id="femaleTECEditInputs" class="space-y-2">
                                <!-- Female TEC edit inputs will be populated by JavaScript -->
                            </div>
                        </div>
                    </div>
                </div>
                
                <!-- JACKET Section -->
                <div class="md:col-span-2 bg-gray-50 p-4 rounded-lg border-2 border-gray-300 mt-4">
                    <h4 class="text-lg sm:text-xl font-bold text-gray-800 mb-4 text-center">🧥 JACKET</h4>
                    <div id="jacketEditInputs" class="grid grid-cols-2 sm:grid-cols-4 gap-3">
                        <!-- JACKET edit inputs will be populated by JavaScript -->
                    </div>
                </div>
                </div>
                
                <div class="flex justify-end gap-3 mt-6">
                    <button id="cancelEdit" class="px-6 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-all">ปิด</button>
                </div>
            </div>
        </div>

        <!-- Transaction History -->
        <div class="mt-8 bg-white/20 backdrop-blur-sm rounded-2xl p-6 card-shadow">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 gap-4">
                <h2 class="text-2xl font-bold text-white flex items-center">
                    <span class="floating-icon mr-2">📋</span> ประวัติการเบิก
                </h2>
                
                <!-- Filter Controls -->
                <div class="flex flex-col sm:flex-row gap-3 w-full sm:w-auto">
                    <select id="historyTypeFilter" class="px-3 py-2 rounded-lg bg-white/90 text-gray-800 font-medium focus:ring-2 focus:ring-purple-500 focus:outline-none text-sm">
                        <option value="all">ทุกประเภท</option>
                        <option value="male">👨 ผู้ชาย</option>
                        <option value="female">👩 ผู้หญิง</option>
                        <option value="malePD">👨 ผู้ชายปัก PD.</option>
                        <option value="femalePD">👩 ผู้หญิงปัก PD.</option>
                        <option value="maleMT">👨 ผู้ชายปัก MT.</option>
                        <option value="maleENG">👨 ผู้ชายปัก ENG</option>
                        <option value="femaleENG">👩 ผู้หญิงปัก ENG</option>
                        <option value="maleTEC">👨 ผู้ชายปัก TEC.</option>
                        <option value="femaleTEC">👩 ผู้หญิงปัก TEC.</option>
                        <option value="jacket">🧥 JACKET</option>
                    </select>
                    
                    <input type="text" id="historyNameFilter" placeholder="ค้นหาชื่อผู้เบิก..." 
                           class="px-3 py-2 rounded-lg bg-white/90 text-gray-800 font-medium focus:ring-2 focus:ring-purple-500 focus:outline-none text-sm">
                    
                    <input type="date" id="historyDateFilter" 
                           class="px-3 py-2 rounded-lg bg-white/90 text-gray-800 font-medium focus:ring-2 focus:ring-purple-500 focus:outline-none text-sm">
                    
                    <button id="clearFilters" class="px-4 py-2 bg-red-500/80 hover:bg-red-600/80 text-white rounded-lg transition-all text-sm whitespace-nowrap">
                        🗑️ ล้างตัวกรอง
                    </button>
                </div>
            </div>
            
            <!-- Summary Stats -->
            <div id="transactionSummary" class="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-6">
                <!-- Summary will be populated by JavaScript -->
            </div>
            
            <div id="transactionHistory" class="space-y-2">
                <p class="text-white/80">ยังไม่มีการเบิกเสื้อ</p>
            </div>
        </div>
    </div>

    <script>
        // Firebase Configuration
        const firebaseConfig = {
            // Replace with your Firebase config
            apiKey: "your-api-key-here",
            authDomain: "your-project.firebaseapp.com",
            databaseURL: "https://your-project-default-rtdb.firebaseio.com/",
            projectId: "your-project-id",
            storageBucket: "your-project.appspot.com",
            messagingSenderId: "123456789",
            appId: "your-app-id"
        };

        // Initialize Firebase
        let firebaseApp, database;
        let useFirebase = false;
        
        try {
            // Check if Firebase config is properly set
            if (firebaseConfig.apiKey !== "your-api-key-here") {
                firebaseApp = firebase.initializeApp(firebaseConfig);
                database = firebase.database();
                useFirebase = true;
                console.log('🔥 Firebase initialized successfully');
            } else {
                console.log('📱 Using localStorage mode (Firebase not configured)');
            }
        } catch (error) {
            console.error('Firebase initialization error:', error);
            console.log('📱 Falling back to localStorage mode');
            useFirebase = false;
        }

        // Default stock data
        const defaultStock = {
            male: {
                'S': 30,
                'M': 45,
                'L': 40,
                'XL': 35,
                '2XL': 25,
                '3XL': 20,
                '4XL': 15,
                '5XL': 10
            },
            female: {
                'SS': 20,
                'S': 35,
                'M': 50,
                'L': 40,
                'XL': 30,
                '2XL': 25,
                '3XL': 15,
                '4XL': 10
            },
            malePD: {
                'S': 25,
                'M': 35,
                'L': 30,
                'XL': 25,
                '2XL': 20,
                '3XL': 15,
                '4XL': 10,
                '5XL': 8
            },
            femalePD: {
                'SS': 15,
                'S': 25,
                'M': 35,
                'L': 30,
                'XL': 20,
                '2XL': 15,
                '3XL': 10,
                '4XL': 8
            },
            maleMT: {
                'S': 20,
                'M': 30,
                'L': 25,
                'XL': 20,
                '2XL': 15,
                '3XL': 12,
                '4XL': 8,
                '5XL': 5
            },
            maleENG: {
                'S': 22,
                'M': 32,
                'L': 28,
                'XL': 22,
                '2XL': 18,
                '3XL': 14,
                '4XL': 10,
                '5XL': 6
            },
            femaleENG: {
                'SS': 12,
                'S': 20,
                'M': 28,
                'L': 25,
                'XL': 18,
                '2XL': 12,
                '3XL': 8,
                '4XL': 5
            },
            maleTEC: {
                'S': 18,
                'M': 28,
                'L': 24,
                'XL': 20,
                '2XL': 16,
                '3XL': 12,
                '4XL': 8,
                '5XL': 4
            },
            femaleTEC: {
                'SS': 10,
                'S': 18,
                'M': 25,
                'L': 22,
                'XL': 16,
                '2XL': 10,
                '3XL': 6,
                '4XL': 4
            },
            jacket: {
                'SS': 15,
                'S': 25,
                'M': 30,
                'L': 25,
                'XL': 20,
                '2XL': 15,
                '3XL': 12,
                '4XL': 8,
                '5XL': 5
            }
        };

        // Firebase Data Functions
        async function saveToFirebase(path, data) {
            if (!useFirebase) return false;
            
            try {
                await database.ref(path).set(data);
                console.log(`✅ Saved to Firebase: ${path}`);
                return true;
            } catch (error) {
                console.error(`❌ Firebase save error for ${path}:`, error);
                return false;
            }
        }

        async function loadFromFirebase(path) {
            if (!useFirebase) return null;
            
            try {
                const snapshot = await database.ref(path).once('value');
                const data = snapshot.val();
                console.log(`📥 Loaded from Firebase: ${path}`);
                return data;
            } catch (error) {
                console.error(`❌ Firebase load error for ${path}:`, error);
                return null;
            }
        }

        function setupFirebaseListeners() {
            if (!useFirebase) return;
            
            // Listen for stock changes
            database.ref('stock').on('value', (snapshot) => {
                const firebaseStock = snapshot.val();
                if (firebaseStock && JSON.stringify(firebaseStock) !== JSON.stringify(stock)) {
                    console.log('🔄 Stock updated from Firebase');
                    stock = firebaseStock;
                    displayStock();
                    showAutoSaveMessage('🔥 ซิงค์สต๊อกจาก Firebase แล้ว');
                }
            });

            // Listen for transaction changes
            database.ref('transactions').on('value', (snapshot) => {
                const firebaseTransactions = snapshot.val();
                if (firebaseTransactions && JSON.stringify(firebaseTransactions) !== JSON.stringify(transactions)) {
                    console.log('🔄 Transactions updated from Firebase');
                    transactions = firebaseTransactions || [];
                    displayTransactions();
                    showAutoSaveMessage('🔥 ซิงค์ประวัติจาก Firebase แล้ว');
                }
            });
        }

        // Load data from Firebase first, then URL, then localStorage, or use default
        let stock, transactions;
        
        async function initializeData() {
            let dataLoaded = false;
            
            // Try Firebase first
            if (useFirebase) {
                try {
                    const [firebaseStock, firebaseTransactions] = await Promise.all([
                        loadFromFirebase('stock'),
                        loadFromFirebase('transactions')
                    ]);
                    
                    if (firebaseStock && firebaseTransactions !== null) {
                        stock = firebaseStock;
                        transactions = firebaseTransactions || [];
                        dataLoaded = true;
                        console.log('📥 Data loaded from Firebase');
                        showAutoSaveMessage('🔥 โหลดข้อมูลจาก Firebase แล้ว');
                    }
                } catch (error) {
                    console.error('Firebase load error:', error);
                }
            }
            
            // Try URL if Firebase failed
            if (!dataLoaded && loadDataFromURL()) {
                dataLoaded = true;
            }
            
            // Try localStorage if both Firebase and URL failed
            if (!dataLoaded) {
                stock = loadStockData();
                transactions = loadTransactionData();
            }
            
            // Update URL with current data
            updateURL();
            
            // Setup Firebase listeners for real-time updates
            setupFirebaseListeners();
            
            // Display initial data
            displayStock();
            displayTransactions();
            updateLastSavedInfo();
        }

        // Auto-save functions with Firebase and URL sync
        async function saveStockData() {
            try {
                const timestamp = new Date().toISOString();
                
                // Save to localStorage first (immediate backup)
                localStorage.setItem('shirtStock', JSON.stringify(stock));
                localStorage.setItem('lastSaved', timestamp);
                
                // Save to Firebase if available
                if (useFirebase) {
                    await saveToFirebase('stock', stock);
                    await saveToFirebase('lastUpdated', timestamp);
                }
                
                updateLastSavedInfo(); // Update the display immediately
                updateURL(); // Update URL with current data immediately
                broadcastDataUpdate(); // Notify other tabs/windows
                
                if (useFirebase) {
                    showAutoSaveMessage('🔥 บันทึกลง Firebase แล้ว');
                }
            } catch (error) {
                console.error('Error saving stock data:', error);
                showAutoSaveMessage('❌ เกิดข้อผิดพลาดในการบันทึก');
            }
        }

        async function saveTransactionData() {
            try {
                const timestamp = new Date().toISOString();
                
                // Save to localStorage first (immediate backup)
                localStorage.setItem('shirtTransactions', JSON.stringify(transactions));
                localStorage.setItem('lastSaved', timestamp);
                
                // Save to Firebase if available
                if (useFirebase) {
                    await saveToFirebase('transactions', transactions);
                    await saveToFirebase('lastUpdated', timestamp);
                }
                
                updateLastSavedInfo(); // Update the display immediately
                updateURL(); // Update URL with current data immediately
                broadcastDataUpdate(); // Notify other tabs/windows
                
                if (useFirebase) {
                    showAutoSaveMessage('🔥 บันทึกลง Firebase แล้ว');
                }
            } catch (error) {
                console.error('Error saving transaction data:', error);
                showAutoSaveMessage('❌ เกิดข้อผิดพลาดในการบันทึก');
            }
        }

        // Enhanced URL sync functions for real-time updates
        function updateURL() {
            try {
                const data = {
                    stock: stock,
                    transactions: transactions,
                    timestamp: new Date().toISOString(),
                    version: Date.now() // Add version for conflict resolution
                };
                
                // Compress data by encoding to base64
                const jsonString = JSON.stringify(data);
                const encodedData = btoa(encodeURIComponent(jsonString));
                
                // Update URL without page reload
                const newUrl = window.location.pathname + '?data=' + encodedData;
                window.history.replaceState({}, '', newUrl);
                
                // Store current URL data hash for comparison
                window.currentDataHash = btoa(JSON.stringify({stock, transactions}));
                
                console.log('URL updated with latest data at', new Date().toLocaleTimeString());
            } catch (error) {
                console.error('Error updating URL:', error);
            }
        }

        // Broadcast data updates to other tabs/windows
        function broadcastDataUpdate() {
            try {
                const updateEvent = new CustomEvent('dataUpdate', {
                    detail: {
                        stock: stock,
                        transactions: transactions,
                        timestamp: new Date().toISOString()
                    }
                });
                window.dispatchEvent(updateEvent);
                
                // Also use localStorage event for cross-tab communication
                localStorage.setItem('dataUpdateTrigger', Date.now().toString());
            } catch (error) {
                console.error('Error broadcasting data update:', error);
            }
        }

        function loadDataFromURL() {
            try {
                const urlParams = new URLSearchParams(window.location.search);
                const encodedData = urlParams.get('data');
                
                if (encodedData) {
                    const jsonString = decodeURIComponent(atob(encodedData));
                    const data = JSON.parse(jsonString);
                    
                    if (data.stock && data.transactions) {
                        // Always load URL data if it exists (for shared links)
                        const urlTimestamp = new Date(data.timestamp || 0);
                        const localTimestamp = new Date(localStorage.getItem('lastSaved') || 0);
                        const urlVersion = data.version || 0;
                        const localVersion = parseInt(localStorage.getItem('dataVersion') || '0');
                        
                        // Use version number for more accurate comparison
                        if (urlVersion > localVersion || urlTimestamp > localTimestamp) {
                            console.log('Loading data from URL (newer version)');
                            stock = data.stock;
                            transactions = data.transactions;
                            
                            // Save to localStorage with version
                            localStorage.setItem('shirtStock', JSON.stringify(stock));
                            localStorage.setItem('shirtTransactions', JSON.stringify(transactions));
                            localStorage.setItem('lastSaved', data.timestamp);
                            localStorage.setItem('dataVersion', urlVersion.toString());
                            
                            showAutoSaveMessage('🔄 โหลดข้อมูลล่าสุดจาก URL แล้ว');
                            return true;
                        } else if (localVersion > urlVersion) {
                            console.log('Local data is newer, updating URL');
                            updateURL();
                        }
                    }
                }
            } catch (error) {
                console.error('Error loading data from URL:', error);
            }
            return false;
        }

        // Real-time sync system
        function startRealTimeSync() {
            // Listen for storage changes (cross-tab communication)
            window.addEventListener('storage', function(e) {
                if (e.key === 'dataUpdateTrigger') {
                    console.log('Data updated in another tab, syncing...');
                    const newStock = loadStockData();
                    const newTransactions = loadTransactionData();
                    
                    // Check if data actually changed
                    const newDataHash = btoa(JSON.stringify({stock: newStock, transactions: newTransactions}));
                    if (newDataHash !== window.currentDataHash) {
                        stock = newStock;
                        transactions = newTransactions;
                        displayStock();
                        displayTransactions();
                        updateURL();
                        showAutoSaveMessage('🔄 ซิงค์ข้อมูลจากแท็บอื่นแล้ว');
                    }
                }
            });

            // Listen for custom data update events
            window.addEventListener('dataUpdate', function(e) {
                console.log('Received data update event');
            });

            // Periodic URL check for shared links (every 10 seconds)
            setInterval(function() {
                const urlParams = new URLSearchParams(window.location.search);
                const encodedData = urlParams.get('data');
                
                if (encodedData) {
                    try {
                        const jsonString = decodeURIComponent(atob(encodedData));
                        const data = JSON.parse(jsonString);
                        
                        if (data.version) {
                            const urlVersion = data.version;
                            const localVersion = parseInt(localStorage.getItem('dataVersion') || '0');
                            
                            // If URL has newer data, load it
                            if (urlVersion > localVersion) {
                                console.log('Detected newer data in URL, syncing...');
                                if (loadDataFromURL()) {
                                    displayStock();
                                    displayTransactions();
                                    updateLastSavedInfo();
                                    showAutoSaveMessage('🔄 ซิงค์ข้อมูลล่าสุดจาก URL แล้ว');
                                }
                            }
                        }
                    } catch (error) {
                        // Ignore parsing errors
                    }
                }
            }, 10000);

            // Auto-update URL every 10 seconds to ensure it's always current
            setInterval(function() {
                updateURL();
            }, 10000);
        }

        function loadStockData() {
            try {
                const savedStock = localStorage.getItem('shirtStock');
                if (savedStock) {
                    const parsedStock = JSON.parse(savedStock);
                    // Ensure all categories exist (in case new categories were added)
                    Object.keys(defaultStock).forEach(category => {
                        if (!parsedStock[category]) {
                            parsedStock[category] = { ...defaultStock[category] };
                        } else {
                            // Ensure all sizes exist for each category
                            Object.keys(defaultStock[category]).forEach(size => {
                                if (parsedStock[category][size] === undefined) {
                                    parsedStock[category][size] = defaultStock[category][size];
                                }
                            });
                        }
                    });
                    return parsedStock;
                }
            } catch (error) {
                console.error('Error loading stock data:', error);
            }
            return JSON.parse(JSON.stringify(defaultStock)); // Deep copy of default data
        }

        function loadTransactionData() {
            try {
                const savedTransactions = localStorage.getItem('shirtTransactions');
                return savedTransactions ? JSON.parse(savedTransactions) : [];
            } catch (error) {
                console.error('Error loading transaction data:', error);
                return [];
            }
        }

        function showAutoSaveMessage(message = '✅ บันทึกอัตโนมัติแล้ว') {
            // Remove existing auto-save message if any
            const existingMessage = document.querySelector('.auto-save-message');
            if (existingMessage) {
                existingMessage.remove();
            }
            
            // Create new auto-save message
            const messageEl = document.createElement('div');
            messageEl.className = 'auto-save-message fixed top-4 right-4 bg-green-500 text-white px-4 py-2 rounded-lg shadow-lg z-50 transition-all duration-300';
            messageEl.innerHTML = message;
            document.body.appendChild(messageEl);
            
            // Remove message after 2 seconds
            setTimeout(() => {
                messageEl.style.opacity = '0';
                setTimeout(() => {
                    if (messageEl.parentNode) {
                        messageEl.parentNode.removeChild(messageEl);
                    }
                }, 300);
            }, 1500);
        }

        // Display stock function
        function displayStock() {
            displayGenderStock('male', 'maleStockDisplay');
            displayGenderStock('female', 'femaleStockDisplay');
            displayGenderStock('malePD', 'malePDStockDisplay');
            displayGenderStock('femalePD', 'femalePDStockDisplay');
            displayGenderStock('maleMT', 'maleMTStockDisplay');
            displayGenderStock('maleENG', 'maleENGStockDisplay');
            displayGenderStock('femaleENG', 'femaleENGStockDisplay');
            displayGenderStock('maleTEC', 'maleTECStockDisplay');
            displayGenderStock('femaleTEC', 'femaleTECStockDisplay');
            displayGenderStock('jacket', 'jacketStockDisplay');
        }

        function displayGenderStock(gender, containerId) {
            const container = document.getElementById(containerId);
            container.innerHTML = '';
            
            Object.entries(stock[gender]).forEach(([size, quantity]) => {
                const stockItem = document.createElement('div');
                
                // Different colors for different sizes
                let sizeColor = '';
                if (['SS', 'S'].includes(size)) {
                    sizeColor = 'bg-gradient-to-br from-purple-400 to-purple-600';
                } else if (['M', 'L'].includes(size)) {
                    sizeColor = 'bg-gradient-to-br from-blue-400 to-blue-600';
                } else if (['XL', '2XL'].includes(size)) {
                    sizeColor = 'bg-gradient-to-br from-green-400 to-green-600';
                } else {
                    sizeColor = 'bg-gradient-to-br from-orange-400 to-orange-600';
                }
                
                stockItem.className = `size-badge ${sizeColor} backdrop-blur-sm rounded-xl p-2 sm:p-3 text-center border border-white/30 shadow-lg`;
                
                const statusColor = quantity > 20 ? 'text-green-100' : quantity > 10 ? 'text-yellow-100' : 'text-red-100';
                const statusIcon = quantity > 20 ? '✅' : quantity > 10 ? '⚠️' : '🔴';
                
                stockItem.innerHTML = `
                    <div class="text-xs sm:text-base font-bold text-white mb-1">${size}</div>
                    <div class="text-xs ${statusColor} font-semibold">${quantity} ตัว</div>
                    <div class="text-xs">${statusIcon}</div>
                `;
                
                container.appendChild(stockItem);
            });
        }

        // Display transaction history with filters
        function displayTransactions(filteredTransactions = null) {
            const historyDiv = document.getElementById('transactionHistory');
            const summaryDiv = document.getElementById('transactionSummary');
            
            // Use filtered transactions or all transactions
            const transactionsToShow = filteredTransactions || transactions;
            
            // Update summary statistics
            updateTransactionSummary(transactionsToShow);
            
            if (transactionsToShow.length === 0) {
                historyDiv.innerHTML = '<p class="text-white/80 text-center py-8">ไม่พบข้อมูลที่ตรงกับเงื่อนไขการค้นหา</p>';
                return;
            }
            
            // Create responsive table
            let tableHTML = `
                <div class="bg-white/20 rounded-lg overflow-hidden">
                    <!-- Desktop Header -->
                    <div class="hidden md:grid grid-cols-6 gap-4 p-4 bg-white/10 font-bold text-white border-b border-white/20">
                        <div class="text-center">👤 ผู้เบิก</div>
                        <div class="text-center">👕 ประเภท</div>
                        <div class="text-center">📏 ไซต์</div>
                        <div class="text-center">📦 จำนวน</div>
                        <div class="text-center">📝 หมายเหตุ</div>
                        <div class="text-center">📅 วันที่</div>
                    </div>
                    
                    <!-- Mobile/Tablet Header -->
                    <div class="md:hidden grid grid-cols-3 gap-2 p-3 bg-white/10 font-bold text-white border-b border-white/20 text-sm">
                        <div class="text-center">👤 ผู้เบิก</div>
                        <div class="text-center">👕 รายละเอียด</div>
                        <div class="text-center">📅 วันที่</div>
                    </div>
                    
                    <div class="divide-y divide-white/10">
            `;
            
            // Add transaction rows
            tableHTML += transactionsToShow.map((transaction, index) => {
                const typeText = getTypeText(transaction.gender);
                const rowBg = index % 2 === 0 ? 'bg-white/5' : 'bg-white/10';
                
                return `
                    <!-- Desktop Row -->
                    <div class="hidden md:grid grid-cols-6 gap-4 p-4 ${rowBg} hover:bg-white/15 transition-all duration-200">
                        <div class="text-white font-medium text-center">${transaction.withdrawer}</div>
                        <div class="text-white/90 text-center">
                            <div class="text-sm">${typeText}</div>
                        </div>
                        <div class="text-white/90 text-center">
                            <span class="bg-purple-500/30 px-2 py-1 rounded-full text-sm font-bold">${transaction.size}</span>
                        </div>
                        <div class="text-white font-bold text-center">
                            <span class="bg-blue-500/30 px-3 py-1 rounded-full text-sm">${transaction.quantity} ตัว</span>
                        </div>
                        <div class="text-white/80 text-center text-xs">
                            ${transaction.notes ? `<div class="bg-yellow-500/20 px-2 py-1 rounded text-xs max-w-32 mx-auto truncate" title="${transaction.notes}">${transaction.notes}</div>` : '<span class="text-white/50">-</span>'}
                        </div>
                        <div class="text-white/80 text-center text-sm">${transaction.date}</div>
                    </div>
                    
                    <!-- Mobile/Tablet Row -->
                    <div class="md:hidden grid grid-cols-3 gap-2 p-3 ${rowBg} hover:bg-white/15 transition-all duration-200">
                        <div class="text-white font-medium text-center text-sm">${transaction.withdrawer}</div>
                        <div class="text-white/90 text-center">
                            <div class="text-xs">${typeText}</div>
                            <div class="flex justify-center gap-1 mt-1">
                                <span class="bg-purple-500/30 px-2 py-0.5 rounded text-xs">${transaction.size}</span>
                                <span class="bg-blue-500/30 px-2 py-0.5 rounded text-xs">${transaction.quantity}ตัว</span>
                            </div>
                            ${transaction.notes ? `<div class="bg-yellow-500/20 px-1 py-0.5 rounded text-xs mt-1 truncate max-w-full" title="${transaction.notes}">📝 ${transaction.notes}</div>` : ''}
                        </div>
                        <div class="text-white/80 text-center text-xs">${transaction.date}</div>
                    </div>
                `;
            }).join('');
            
            tableHTML += `
                    </div>
                </div>
            `;
            
            historyDiv.innerHTML = tableHTML;
        }
        
        // Helper function to get type text
        function getTypeText(gender) {
            const typeMap = {
                'male': 'ผู้ชาย',
                'female': 'ผู้หญิง',
                'malePD': 'ผู้ชายปัก PD.',
                'femalePD': 'ผู้หญิงปัก PD.',
                'maleMT': 'ผู้ชายปัก MT.',
                'maleENG': 'ผู้ชายปัก ENG',
                'femaleENG': 'ผู้หญิงปัก ENG',
                'maleTEC': 'ผู้ชายปัก TEC.',
                'femaleTEC': 'ผู้หญิงปัก TEC.',
                'jacket': 'JACKET'
            };
            return typeMap[gender] || gender;
        }
        
        // Update transaction summary
        function updateTransactionSummary(transactionsToShow) {
            const summaryDiv = document.getElementById('transactionSummary');
            
            if (transactionsToShow.length === 0) {
                summaryDiv.innerHTML = '';
                return;
            }
            
            // Calculate statistics
            const totalTransactions = transactionsToShow.length;
            const totalQuantity = transactionsToShow.reduce((sum, t) => sum + t.quantity, 0);
            const uniqueWithdrawers = new Set(transactionsToShow.map(t => t.withdrawer)).size;
            
            // Most popular size
            const sizeCount = {};
            transactionsToShow.forEach(t => {
                sizeCount[t.size] = (sizeCount[t.size] || 0) + t.quantity;
            });
            const mostPopularSize = Object.keys(sizeCount).reduce((a, b) => sizeCount[a] > sizeCount[b] ? a : b, '');
            
            summaryDiv.innerHTML = `
                <div class="bg-blue-500/20 rounded-lg p-3 text-center">
                    <div class="text-white/90 text-xs mb-1">รายการทั้งหมด</div>
                    <div class="text-white font-bold text-lg">${totalTransactions}</div>
                </div>
                <div class="bg-green-500/20 rounded-lg p-3 text-center">
                    <div class="text-white/90 text-xs mb-1">จำนวนรวม</div>
                    <div class="text-white font-bold text-lg">${totalQuantity} ตัว</div>
                </div>
                <div class="bg-purple-500/20 rounded-lg p-3 text-center">
                    <div class="text-white/90 text-xs mb-1">ผู้เบิก</div>
                    <div class="text-white font-bold text-lg">${uniqueWithdrawers} คน</div>
                </div>
                <div class="bg-orange-500/20 rounded-lg p-3 text-center">
                    <div class="text-white/90 text-xs mb-1">ไซต์ยอดนิยม</div>
                    <div class="text-white font-bold text-lg">${mostPopularSize || '-'}</div>
                </div>
            `;
        }
        
        // Filter transactions
        function filterTransactions() {
            const typeFilter = document.getElementById('historyTypeFilter').value;
            const nameFilter = document.getElementById('historyNameFilter').value.toLowerCase().trim();
            const dateFilter = document.getElementById('historyDateFilter').value;
            
            let filtered = transactions;
            
            // Filter by type
            if (typeFilter !== 'all') {
                filtered = filtered.filter(t => t.gender === typeFilter);
            }
            
            // Filter by name
            if (nameFilter) {
                filtered = filtered.filter(t => t.withdrawer.toLowerCase().includes(nameFilter));
            }
            
            // Filter by date
            if (dateFilter) {
                const filterDate = new Date(dateFilter);
                const filterDateStr = filterDate.toLocaleDateString('th-TH', {
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                });
                filtered = filtered.filter(t => t.date === filterDateStr);
            }
            
            displayTransactions(filtered);
        }

        // Edit stock functions
        function openEditStockModal() {
            const modal = document.getElementById('editStockModal');
            modal.classList.remove('hidden');
            populateEditInputs();
        }

        function closeEditStockModal() {
            const modal = document.getElementById('editStockModal');
            modal.classList.add('hidden');
        }

        function populateEditInputs() {
            const containers = {
                'male': document.getElementById('maleEditInputs'),
                'female': document.getElementById('femaleEditInputs'),
                'malePD': document.getElementById('malePDEditInputs'),
                'femalePD': document.getElementById('femalePDEditInputs'),
                'maleMT': document.getElementById('maleMTEditInputs'),
                'maleENG': document.getElementById('maleENGEditInputs'),
                'femaleENG': document.getElementById('femaleENGEditInputs'),
                'maleTEC': document.getElementById('maleTECEditInputs'),
                'femaleTEC': document.getElementById('femaleTECEditInputs'),
                'jacket': document.getElementById('jacketEditInputs')
            };
            
            // Clear all containers
            Object.values(containers).forEach(container => container.innerHTML = '');
            
            // Populate all categories
            Object.entries(stock).forEach(([category, sizes]) => {
                Object.entries(sizes).forEach(([size, quantity]) => {
                    const inputDiv = document.createElement('div');
                    inputDiv.className = 'flex items-center gap-2';
                    inputDiv.innerHTML = `
                        <label class="w-10 text-gray-700 font-medium text-sm">${size}:</label>
                        <input type="number" min="0" value="${quantity}" 
                               class="flex-1 px-3 py-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 focus:outline-none text-sm auto-save-input"
                               data-gender="${category}" data-size="${size}">
                        <span class="text-gray-500 text-sm">ตัว</span>
                    `;
                    containers[category].appendChild(inputDiv);
                });
            });
            
            // Add auto-save functionality to all inputs
            document.querySelectorAll('.auto-save-input').forEach(input => {
                input.addEventListener('input', function() {
                    const gender = this.dataset.gender;
                    const size = this.dataset.size;
                    const newQuantity = parseInt(this.value) || 0;
                    
                    // Update stock immediately
                    stock[gender][size] = newQuantity;
                    
                    // Save to localStorage immediately
                    saveStockData();
                    
                    // Update the main display
                    displayStock();
                    
                    // Show brief save confirmation
                    showAutoSaveMessage('💾 บันทึกสต๊อกอัตโนมัติแล้ว');
                });
            });
        }

        function saveStockChanges() {
            const inputs = document.querySelectorAll('#editStockModal input[type="number"]');
            
            inputs.forEach(input => {
                const gender = input.dataset.gender;
                const size = input.dataset.size;
                const newQuantity = parseInt(input.value) || 0;
                stock[gender][size] = newQuantity;
            });
            
            displayStock();
            closeEditStockModal();
            showMessage('อัพเดทจำนวนสต๊อกเรียบร้อยแล้ว', 'success');
        }

        // Show message function
        function showMessage(message, type) {
            const messageDiv = document.getElementById('resultMessage');
            messageDiv.className = `mt-6 p-4 rounded-xl ${type === 'success' ? 'bg-green-500/20 border border-green-400 text-green-100' : 'bg-red-500/20 border border-red-400 text-red-100'}`;
            messageDiv.textContent = message;
            messageDiv.classList.remove('hidden');
            
            setTimeout(() => {
                messageDiv.classList.add('hidden');
            }, 3000);
        }

        // Function to check if all form fields are filled
        function checkFormValidity() {
            const gender = document.getElementById('gender').value;
            const size = document.getElementById('size').value;
            const withdrawer = document.getElementById('withdrawer').value.trim();
            const withdrawDate = document.getElementById('withdrawDate').value;
            const quantity = document.getElementById('quantity').value;
            
            const submitBtn = document.getElementById('submitBtn');
            
            if (gender && size && withdrawer && withdrawDate && quantity && parseInt(quantity) > 0) {
                // All fields are filled - enable button
                submitBtn.disabled = false;
                submitBtn.className = "submit-btn-enabled w-full text-white font-bold py-4 px-6 rounded-xl hover:scale-105 transform transition-all duration-300 shadow-lg cursor-pointer";
            } else {
                // Some fields are missing - disable button
                submitBtn.disabled = true;
                submitBtn.className = "w-full bg-gray-400 text-gray-600 font-bold py-4 px-6 rounded-xl cursor-not-allowed transition-all duration-300 shadow-lg";
            }
        }

        // Handle gender selection
        document.getElementById('gender').addEventListener('change', function() {
            const gender = this.value;
            const sizeSelect = document.getElementById('size');
            
            sizeSelect.innerHTML = '<option value="">เลือกไซต์</option>';
            
            if (gender) {
                sizeSelect.disabled = false;
                Object.keys(stock[gender]).forEach(size => {
                    const option = document.createElement('option');
                    option.value = size;
                    option.textContent = size;
                    sizeSelect.appendChild(option);
                });
            } else {
                sizeSelect.disabled = true;
            }
            
            checkFormValidity();
        });

        // Set default date to today
        document.getElementById('withdrawDate').valueAsDate = new Date();

        // Add event listeners to all form fields to check validity in real-time
        document.getElementById('size').addEventListener('change', checkFormValidity);
        document.getElementById('withdrawer').addEventListener('input', checkFormValidity);
        document.getElementById('withdrawDate').addEventListener('change', checkFormValidity);
        document.getElementById('quantity').addEventListener('input', checkFormValidity);

        // Handle form submission
        document.getElementById('withdrawForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const gender = document.getElementById('gender').value;
            const size = document.getElementById('size').value;
            const withdrawer = document.getElementById('withdrawer').value.trim();
            const withdrawDate = document.getElementById('withdrawDate').value;
            const quantity = parseInt(document.getElementById('quantity').value);
            const notes = document.getElementById('notes').value.trim();
            
            // Validation
            if (!gender) {
                showMessage('กรุณาเลือกประเภทเสื้อ', 'error');
                return;
            }
            
            if (!size) {
                showMessage('กรุณาเลือกไซต์เสื้อ', 'error');
                return;
            }
            
            if (!withdrawer) {
                showMessage('กรุณากรอกชื่อผู้เบิก', 'error');
                return;
            }
            
            if (!withdrawDate) {
                showMessage('กรุณาเลือกวันที่เบิก', 'error');
                return;
            }
            
            if (!quantity || quantity <= 0) {
                showMessage('กรุณากรอกจำนวนที่ถูกต้อง', 'error');
                return;
            }
            
            if (stock[gender][size] < quantity) {
                const typeText = gender === 'male' ? 'ผู้ชาย' : 
                               gender === 'female' ? 'ผู้หญิง' : 
                               gender === 'malePD' ? 'ผู้ชายปัก PD.' : 
                               gender === 'femalePD' ? 'ผู้หญิงปัก PD.' :
                               gender === 'maleMT' ? 'ผู้ชายปัก MT.' :
                               gender === 'maleENG' ? 'ผู้ชายปัก ENG' :
                               gender === 'femaleENG' ? 'ผู้หญิงปัก ENG' :
                               gender === 'maleTEC' ? 'ผู้ชายปัก TEC.' : 
                               gender === 'femaleTEC' ? 'ผู้หญิงปัก TEC.' : 'JACKET';
                showMessage(`เสื้อ${typeText} ไซต์ ${size} มีเพียง ${stock[gender][size]} ตัว ไม่เพียงพอสำหรับการเบิก`, 'error');
                return;
            }
            
            // Process withdrawal
            stock[gender][size] -= quantity;
            
            // Format date for display
            const dateObj = new Date(withdrawDate);
            const formattedDate = dateObj.toLocaleDateString('th-TH', {
                year: 'numeric',
                month: 'long',
                day: 'numeric'
            });
            
            // Add to transaction history
            transactions.unshift({
                gender: gender,
                size: size,
                quantity: quantity,
                withdrawer: withdrawer,
                date: formattedDate,
                notes: notes, // Add notes field
                timestamp: new Date().toISOString() // Add timestamp for sorting
            });
            
            // Save both stock and transaction data immediately
            saveStockData();
            saveTransactionData();
            
            // Update displays
            displayStock();
            displayTransactions();
            
            // Show auto-save confirmation
            showAutoSaveMessage('💾 บันทึกการเบิกอัตโนมัติแล้ว');
            
            // Show success message
            const typeText = gender === 'male' ? 'ผู้ชาย' : 
                           gender === 'female' ? 'ผู้หญิง' : 
                           gender === 'malePD' ? 'ผู้ชายปัก PD.' : 
                           gender === 'femalePD' ? 'ผู้หญิงปัก PD.' :
                           gender === 'maleMT' ? 'ผู้ชายปัก MT.' :
                           gender === 'maleENG' ? 'ผู้ชายปัก ENG' :
                           gender === 'femaleENG' ? 'ผู้หญิงปัก ENG' :
                           gender === 'maleTEC' ? 'ผู้ชายปัก TEC.' : 
                           gender === 'femaleTEC' ? 'ผู้หญิงปัก TEC.' : 'JACKET';
            showMessage(`${withdrawer} เบิกเสื้อ${typeText} ไซต์ ${size} จำนวน ${quantity} ตัว สำเร็จ! คงเหลือ ${stock[gender][size]} ตัว`, 'success');
            
            // Reset form
            document.getElementById('withdrawForm').reset();
            document.getElementById('size').disabled = true;
            document.getElementById('withdrawDate').valueAsDate = new Date();
            checkFormValidity(); // Check form validity after reset
        });

        // Event listeners for edit stock modal
        document.getElementById('editStockBtn').addEventListener('click', openEditStockModal);
        document.getElementById('closeEditModal').addEventListener('click', closeEditStockModal);
        document.getElementById('cancelEdit').addEventListener('click', closeEditStockModal);

        // Close modal when clicking outside
        document.getElementById('editStockModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeEditStockModal();
            }
        });

        // Category filter functionality
        document.getElementById('categoryFilter').addEventListener('change', function() {
            const selectedCategory = this.value;
            const allCategoriesView = document.getElementById('allCategoriesView');
            const singleCategoryView = document.getElementById('singleCategoryView');
            const singleCategoryContent = document.getElementById('singleCategoryContent');
            
            if (selectedCategory === 'all') {
                allCategoriesView.classList.remove('hidden');
                singleCategoryView.classList.add('hidden');
            } else {
                allCategoriesView.classList.add('hidden');
                singleCategoryView.classList.remove('hidden');
                
                // Display single category
                const categoryNames = {
                    'male': '👨 ไซต์ผู้ชาย',
                    'female': '👩 ไซต์ผู้หญิง',
                    'malePD': '👨 ไซต์ผู้ชายปัก PD.',
                    'femalePD': '👩 ไซต์ผู้หญิงปัก PD.',
                    'maleMT': '👨 ไซต์ผู้ชายปัก MT.',
                    'maleENG': '👨 ไซต์ผู้ชายปัก ENG',
                    'femaleENG': '👩 ไซต์ผู้หญิงปัก ENG',
                    'maleTEC': '👨 ไซต์ผู้ชายปัก TEC.',
                    'femaleTEC': '👩 ไซต์ผู้หญิงปัก TEC.',
                    'jacket': '🧥 JACKET'
                };
                
                const isFemaleCat = selectedCategory.includes('female');
                const isJacket = selectedCategory === 'jacket';
                const bgColor = isJacket ? 'bg-gradient-to-br from-gray-100 to-gray-200' : 
                               isFemaleCat ? 'bg-gradient-to-br from-pink-100 to-pink-200' : 
                               'bg-gradient-to-br from-blue-100 to-blue-200';
                const textColor = isJacket ? 'text-gray-800' : 
                                 isFemaleCat ? 'text-pink-800' : 'text-blue-800';
                
                singleCategoryContent.innerHTML = `
                    <div class="${bgColor} rounded-lg p-4">
                        <h3 class="text-xl sm:text-2xl font-bold ${textColor} mb-4 text-center">${categoryNames[selectedCategory]}</h3>
                        <div class="grid grid-cols-4 sm:grid-cols-6 lg:grid-cols-8 gap-2 justify-items-center" id="singleCategoryDisplay">
                        </div>
                    </div>
                `;
                
                displayGenderStock(selectedCategory, 'singleCategoryDisplay');
            }
        });

        // Function to update last saved info and Firebase status
        function updateLastSavedInfo() {
            const lastSaved = localStorage.getItem('lastSaved');
            const lastSavedInfo = document.getElementById('lastSavedInfo');
            const firebaseStatus = document.getElementById('firebaseStatus');
            
            if (lastSaved) {
                const savedDate = new Date(lastSaved);
                const now = new Date();
                const diffMinutes = Math.floor((now - savedDate) / (1000 * 60));
                
                let timeText = '';
                if (diffMinutes < 1) {
                    timeText = 'เมื่อสักครู่';
                } else if (diffMinutes < 60) {
                    timeText = `${diffMinutes} นาทีที่แล้ว`;
                } else if (diffMinutes < 1440) {
                    const hours = Math.floor(diffMinutes / 60);
                    timeText = `${hours} ชั่วโมงที่แล้ว`;
                } else {
                    timeText = savedDate.toLocaleDateString('th-TH', {
                        year: 'numeric',
                        month: 'short',
                        day: 'numeric',
                        hour: '2-digit',
                        minute: '2-digit'
                    });
                }
                
                lastSavedInfo.innerHTML = `💾 บันทึกล่าสุด: ${timeText}`;
            } else {
                lastSavedInfo.innerHTML = '💾 บันทึกอัตโนมัติทุกครั้งที่มีการเปลี่ยนแปลง';
            }
            
            // Update Firebase status
            if (useFirebase) {
                firebaseStatus.innerHTML = '🔥 เชื่อมต่อ Firebase - ซิงค์แบบเรียลไทม์';
                firebaseStatus.className = 'text-green-300 text-xs mt-1';
            } else {
                firebaseStatus.innerHTML = '📱 โหมด localStorage (Firebase ยังไม่ได้ตั้งค่า)';
                firebaseStatus.className = 'text-white/70 text-xs mt-1';
            }
        }

        // Filter event listeners
        document.getElementById('historyTypeFilter').addEventListener('change', filterTransactions);
        document.getElementById('historyNameFilter').addEventListener('input', filterTransactions);
        document.getElementById('historyDateFilter').addEventListener('change', filterTransactions);
        
        // Clear filters button
        document.getElementById('clearFilters').addEventListener('click', function() {
            document.getElementById('historyTypeFilter').value = 'all';
            document.getElementById('historyNameFilter').value = '';
            document.getElementById('historyDateFilter').value = '';
            displayTransactions(); // Show all transactions
        });

        // Initialize the application
        initializeData().then(() => {
            console.log('✅ Application initialized successfully');
            
            // Start real-time synchronization system
            startRealTimeSync();
        }).catch(error => {
            console.error('❌ Application initialization error:', error);
            
            // Fallback initialization
            stock = loadStockData();
            transactions = loadTransactionData();
            displayStock();
            displayTransactions();
            updateLastSavedInfo();
            updateURL();
            startRealTimeSync();
        });
        
        // Update last saved info every minute
        setInterval(updateLastSavedInfo, 60000);
        
        // Check for URL changes (when someone shares a link with different data)
        window.addEventListener('popstate', function(event) {
            if (loadDataFromURL()) {
                displayStock();
                displayTransactions();
                updateLastSavedInfo();
            }
        });
        
        // Enhanced page visibility handling for better sync
        document.addEventListener('visibilitychange', function() {
            if (!document.hidden) {
                // Page became visible, check for updates immediately
                console.log('Page became visible, checking for updates...');
                if (loadDataFromURL()) {
                    displayStock();
                    displayTransactions();
                    updateLastSavedInfo();
                    showAutoSaveMessage('🔄 ซิงค์ข้อมูลล่าสุดแล้ว');
                }
            }
        });
        
        // Handle page focus for real-time updates
        window.addEventListener('focus', function() {
            console.log('Window focused, syncing data...');
            if (loadDataFromURL()) {
                displayStock();
                displayTransactions();
                updateLastSavedInfo();
                showAutoSaveMessage('🔄 ซิงค์ข้อมูลจากคอมเครื่องอื่นแล้ว');
            }
        });
        
        // Share button functionality
        document.getElementById('shareBtn').addEventListener('click', function() {
            try {
                const currentUrl = window.location.href;
                
                // Copy to clipboard
                if (navigator.clipboard && navigator.clipboard.writeText) {
                    navigator.clipboard.writeText(currentUrl).then(function() {
                        showAutoSaveMessage('📋 คัดลอกลิงก์แล้ว! เปิดใน Chrome คอมเครื่องไหนก็ซิงค์ข้อมูลอัตโนมัติ');
                    }).catch(function() {
                        fallbackCopyToClipboard(currentUrl);
                    });
                } else {
                    fallbackCopyToClipboard(currentUrl);
                }
            } catch (error) {
                console.error('Error copying to clipboard:', error);
                showAutoSaveMessage('❌ ไม่สามารถคัดลอกลิงก์ได้');
            }
        });
        
        // Fallback copy function for older browsers
        function fallbackCopyToClipboard(text) {
            const textArea = document.createElement('textarea');
            textArea.value = text;
            textArea.style.position = 'fixed';
            textArea.style.left = '-999999px';
            textArea.style.top = '-999999px';
            document.body.appendChild(textArea);
            textArea.focus();
            textArea.select();
            
            try {
                const successful = document.execCommand('copy');
                if (successful) {
                    showAutoSaveMessage('📋 คัดลอกลิงก์แล้ว! เปิดใน Chrome คอมเครื่องไหนก็ซิงค์ข้อมูลอัตโนมัติ');
                } else {
                    showAutoSaveMessage('❌ ไม่สามารถคัดลอกลิงก์ได้');
                }
            } catch (err) {
                showAutoSaveMessage('❌ ไม่สามารถคัดลอกลิงก์ได้');
            }
            
            document.body.removeChild(textArea);
        }
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'96d4e8fc742240f5',t:'MTc1NDg4NTcwOC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
