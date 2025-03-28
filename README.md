paste into browser console: 




(function() {
    let hasConfirmedScore = false; // Flag để nhấn "Confirm Score" một lần
    let hasTriedAgain = false; // Flag để nhấn "Try Again" một lần

    // Hàm lấy các nút điều khiển từ DOM
    function getControlButtons() {
        const basePath = 'body > div.fixed.inset-0.flex.flex-col.overflow-hidden.bg-cover.bg-center > main > div.pointer-events-none.absolute.inset-0.flex.px-4.py-1 > div > div > div.relative.flex.w-full.flex-1.flex-col.items-start.justify-center > div > div > div > div.jsx-2883dceedf364fd2.mb-4.flex.justify-center.md\\:hidden > div';
        const buttons = {
            up: document.querySelector(`${basePath} > div > button`),
            down: document.querySelector(`${basePath} > button:nth-child(3)`),
            left: document.querySelector(`${basePath} > button:nth-child(2)`),
            right: document.querySelector(`${basePath} > button:nth-child(4)`),
        };
        if (!buttons.up || !buttons.down || !buttons.left || !buttons.right) {
            console.log('Không tìm thấy một hoặc nhiều nút điều khiển.');
            return null;
        }
        return buttons;
    }

    // Hàm lấy nút "Try Again" và "Confirm Score" từ overlay
    function getOverlayButtons() {
        const overlayPath = '#game-container > div.jsx-2883dceedf364fd2.duration-400.absolute.inset-0.z-10.flex.flex-col.items-center.justify-center.rounded.bg-white\\/90.backdrop-blur-sm.transition-all';
        const tryAgainButton = document.querySelector(`${overlayPath} > div:nth-child(4) > button`);
        const confirmScoreButton = document.querySelector(`${overlayPath} button.rounded-md.bg-succinct-pink.text-white`);
        console.log('Tìm nút "Try Again":', tryAgainButton ? 'Có' : 'Không');
        console.log('Tìm nút "Confirm Score":', confirmScoreButton ? 'Có' : 'Không');
        return { tryAgain: tryAgainButton, confirmScore: confirmScoreButton };
    }

    // Hàm nhấn nút ngẫu nhiên trong số 4 nút điều khiển
    function pressRandomControlButton(buttons) {
        const directions = ['up', 'down', 'left', 'right'];
        const randomDirection = directions[Math.floor(Math.random() * directions.length)];
        const button = buttons[randomDirection];
        if (button) {
            button.click();
            console.log(`Đã nhấn nút điều khiển: ${randomDirection}`);
        } else {
            console.log(`Nút ${randomDirection} không khả dụng.`);
        }
    }

    // Logic bot chính
    function autoPlay() {
        const controlButtons = getControlButtons();
        const overlayButtons = getOverlayButtons();
        if (!controlButtons) {
            console.log('Không thể khởi động bot do thiếu nút điều khiển.');
            return;
        }

        const overlay = document.querySelector('#game-container > div.jsx-2883dceedf364fd2.duration-400.absolute.inset-0.z-10');
        if (overlay && overlay.offsetParent !== null) { // Kiểm tra overlay hiển thị
            const overlayText = overlay.textContent.toLowerCase();
            console.log('Overlay hiển thị với nội dung:', overlayText);
            if (overlayText.includes('game over')) {
                if (overlayButtons.confirmScore && !hasConfirmedScore) {
                    overlayButtons.confirmScore.click();
                    hasConfirmedScore = true;
                    console.log('Đã nhấn "Confirm Score" (một lần duy nhất)');
                    return;
                } else if (overlayButtons.tryAgain && !hasTriedAgain) {
                    overlayButtons.tryAgain.click();
                    hasTriedAgain = true;
                    console.log('Đã nhấn "Try Again" (một lần duy nhất)');
                    return;
                } else {
                    console.log('Không tìm thấy "Confirm Score" hoặc "Try Again" để nhấn.');
                }
            }
        } else {
            // Reset flags khi overlay không hiển thị (game tiếp tục)
            hasConfirmedScore = false;
            hasTriedAgain = false;
            console.log('Game đang chạy, nhấn nút điều khiển...');
            pressRandomControlButton(controlButtons);
        }
    }

    // Chạy bot liên tục
    console.log('Bot nhấn ngẫu nhiên 4 nút và xử lý overlay 2048 (đảm bảo nhấn Confirm Score) đã khởi động!');
    const intervalId = setInterval(autoPlay, 300); // 300ms để chờ animation

    // Dừng bot
    window.stopBot = function() {
        clearInterval(intervalId);
        console.log('Bot đã dừng.');
    };
})();
